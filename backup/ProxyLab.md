
> 使用信号量协调对共享资源的访问
> Using Semaphores to Coordinate Access to Shared Resources

1. 基本思想：线程使用信号量操作来通知另一个线程某个条件已变为真
2. Basic idea: Thread uses a semaphore operation to notify another thread that some condition has become true
3. 使用计数信号量来跟踪资源状态并通知其他线程
4. Use counting semaphores to keep track of resource state and to notify other threads
5. 使用互斥锁来保护对资源的访问
6. Use mutex to protect access to resource

> 两种典型例子

1. The Producer-­Consumer Problem 
2. The Readers-­Writers Problem

## The Producer­‐Consumer

[[25-sync-advanced.pdf#page=4&selection=0,15,10,26|25-sync-advanced, 页面 4]]

## The Readers-­Writers Problem

[[25-sync-advanced.pdf#page=11&selection=0,15,8,25|25-sync-advanced, 页面 11]]

## 线程安全函数

> 如果一个函数在被多个并发线程重复调用时总能产生正确的结果，那么它就是线程安全的

## 线程不安全函数

1. 使用不安全的函数
2. 函数返回静态指针变量     
3. 函数不保护共享变量的访问
4. 函数维持状态通过多次调用 （操作静态变量）

## reentrant 

> 可重入函数： 属于线程安全函数，不需要同步操作，不访问共享变量。

## Race Example

## Deadlocking Example