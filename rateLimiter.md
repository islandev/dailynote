1. 初始化方法 
```java
 public static RateLimiter create(double permitsPerSecond) {
    /*
     * The default RateLimiter configuration can save the unused permits(许可) of up to one second(只会存储最多一秒的许可,为了避免T3等待3S).
     * This is to avoid unnecessary stalls(暂停) in situations like this: A RateLimiter of 1qps,
     * and 4 threads, all calling acquire() at these moments:
     *
     * T0 at 0 seconds
     * T1 at 1.05 seconds
     * T2 at 2 seconds
     * T3 at 3 seconds
     *
     * Due to the slight delay of T1, T2 would have to sleep till 2.05 seconds,
     * and T3 would also have to sleep till 3.05 seconds.
     */
    return create(SleepingStopwatch.createFromSystemTimer(), permitsPerSecond);
  }
```

2. SleepingStopwatch实现
    1. 调用此对象时 都会 获得互斥锁(mutex)
    2. 初始化方法
    ```java
     static final SleepingStopwatch createFromSystemTimer() {
      return new SleepingStopwatch() {
        //返回一个stopwatch对象
        final Stopwatch stopwatch = Stopwatch.createStarted();
        
        @Override
        long readMicros() {
          return stopwatch.elapsed(MICROSECONDS);
        }

        @Override
        void sleepMicrosUninterruptibly(long micros) {
          if (micros > 0) {
              //不间断的唤起sleep
            Uninterruptibles.sleepUninterruptibly(micros, MICROSECONDS);
          }
        }
      };

    ```
3. stopwatch实现
    1. 初始化方法
    An object that measures elapsed time in nanoseconds,一个已纳秒去度量 方法消耗时间
    ```java
    //返回一个新的 对象
     public static Stopwatch createStarted() {
    return new Stopwatch().start();
    }
    //返回一个ticker对象
    @Deprecated
    Stopwatch() {
        this(Ticker.systemTicker());
    }
     public static Ticker systemTicker() {
    return SYSTEM_TICKER;
    }
    //返回系统的时间
    private static final Ticker SYSTEM_TICKER = new Ticker() {
        @Override
        public long read() {
        return Platform.systemNanoTime();
        }
    };
    //stopwatch.elapsed(MICROSECONDS)
    //如果还在运行 就返回  时间差+花费的时间
    public long elapsed(TimeUnit desiredUnit) {
        return desiredUnit.convert(elapsedNanos(), NANOSECONDS);
    }
    private long elapsedNanos() {
        return isRunning ? ticker.read() - startTick + elapsedNanos : elapsedNanos;
    }


    ```
4. ticker对象
    1. 一个始终对象，只能返回 elapsed time，而不是wall  time
    2. 只能通过stop watch 来调用

5. Uninterruptibles
    
    将中断的操作当做不间断处理的工具类
    Utilities for treating interruptible operations as uninterruptible.
    1. 涉及的代码
    ```java
    //不间断的唤起sleep
    public static void sleepUninterruptibly(long sleepFor, TimeUnit unit) {
    boolean interrupted = false;
    try {
      long remainingNanos = unit.toNanos(sleepFor);
      long end = System.nanoTime() + remainingNanos;
      //一直等待 等到线程可以中断 将线程中断
      while (true) {
        try {
          // TimeUnit.sleep() treats negative timeouts just like zero.
          //当前线程sleep  若
          NANOSECONDS.sleep(remainingNanos);
          return;
        } catch (InterruptedException e) {
          interrupted = true;
          remainingNanos = end - System.nanoTime();
        }
      }
    } finally {
    //表明线程可以中断 将线程中断
      if (interrupted) {
        Thread.currentThread().interrupt();
             }
        }
    }
    //当前的thread sleep 如果抛出 InterruptedException则证明他是可以中断的
    public void sleep(long timeout) throws InterruptedException {
        if (timeout > 0) {
            long ms = toMillis(timeout);
            int ns = excessNanos(timeout, ms);
            Thread.sleep(ms, ns);
        }
    }
    ```

6. 初始化方法，初始化一个 检测方法耗时的 类

7. acquire方法阅读
```java
// 每次都会 acquire 1
  public double acquire() {
    return acquire(1);
  }
    //获取指定数量的许可，阻塞  知道 请求被授予，告知线程休眠的时间，如果可以的话
    public double acquire(int permits) {
    long microsToWait = reserve(permits);
    stopwatch.sleepMicrosUninterruptibly(microsToWait);
    return 1.0 * microsToWait / SECONDS.toMicros(1L);
  }

   /**
   * Reserves the given number of permits from this {@code RateLimiter} for future use, returning
   * the number of microseconds until the reservation can be consumed.
   *
   * @return time in microseconds to wait until the resource can be acquired, never negative
   *存储了可以使用的许可，返回 直到许可被消费光的时长
   */
  final long reserve(int permits) {
    checkPermits(permits);
    //在这获取锁 
    synchronized (mutex()) {
      return reserveAndGetWaitLength(permits, stopwatch.readMicros());
    }
  }

    /**
   * Reserves next ticket and returns the wait time that the caller must wait for.
   *
   * @return the required wait time, never negative
   */
  final long reserveAndGetWaitLength(int permits, long nowMicros) {
    long momentAvailable = reserveEarliestAvailable(permits, nowMicros);
    return max(momentAvailable - nowMicros, 0);
  }

    @Override
  final long reserveEarliestAvailable(int requiredPermits, long nowMicros) {
    resync(nowMicros);
    long returnValue = nextFreeTicketMicros;
    double storedPermitsToSpend = min(requiredPermits, this.storedPermits);
    double freshPermits = requiredPermits - storedPermitsToSpend;

    long waitMicros = storedPermitsToWaitTime(this.storedPermits, storedPermitsToSpend)
        + (long) (freshPermits * stableIntervalMicros);

    this.nextFreeTicketMicros = nextFreeTicketMicros + waitMicros;
    this.storedPermits -= storedPermitsToSpend;
    return returnValue;
  }

   private void resync(long nowMicros) {
    // if nextFreeTicket is in the past, resync to now
    if (nowMicros > nextFreeTicketMicros) {
      storedPermits = min(maxPermits,
          storedPermits + (nowMicros - nextFreeTicketMicros) / stableIntervalMicros);
      nextFreeTicketMicros = nowMicros;
    }
  }


      @Override
    long storedPermitsToWaitTime(double storedPermits, double permitsToTake) {
      double availablePermitsAboveHalf = storedPermits - halfPermits;
      long micros = 0;
      // measuring the integral on the right part of the function (the climbing line)
      if (availablePermitsAboveHalf > 0.0) {
        double permitsAboveHalfToTake = min(availablePermitsAboveHalf, permitsToTake);
        micros = (long) (permitsAboveHalfToTake * (permitsToTime(availablePermitsAboveHalf)
            + permitsToTime(availablePermitsAboveHalf - permitsAboveHalfToTake)) / 2.0);
        permitsToTake -= permitsAboveHalfToTake;
      }
      // measuring the integral on the left part of the function (the horizontal line)
      micros += (stableIntervalMicros * permitsToTake);
      return micros;
    }
```
    


