# java集合类

## hashmap
1. 根据key的hashcode 决定其分桶，而后根据equal方法来确定其在组中的位置，在```hash&(leghth-1)```位置

2. resize  根据其负载参数，0.75 其大于12时 进行扩容，rehash
3. fail-fast 
    通过modCount进行判断，
4. 使用entrySet进行遍历  不要是使用keyset

## hashset
1. 基于hashmap，如果使用 要重写hashcode equals方法
2. 

## hashtable
1. 使用拉链法实现的哈希表

2. 和hashmap的区别
    1. hashtable 集成dic类 hashmap集成map类
    2. hashmap允许kv为空 hashtable 不允许
    3. hashtable是可以用来多线程 hashmap不可
    
##  linkedhashmap
1. 与hashmap不同的是 他维护着一个运行于所有条目的双重链表，此链表确定了迭代顺序，确保进出一致


