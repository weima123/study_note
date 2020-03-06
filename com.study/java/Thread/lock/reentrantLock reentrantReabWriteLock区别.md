## 一.Lock接口的实现
### 1.reentrantLock
### 2.reentrantReadWriteLock （内部包含readLock 和writeLock)
## 一.区别
### 1.reentrantLock是拍他锁，某一时刻只有一个线程在访问共享资源，其他线程不论是读
还是写都无法再访问共享资源，资源利用低下
### 2.reentrantReadWriteLock提供了两个锁,读锁(共享锁）和写锁（排他锁）
```
读读共享，读写互斥，写写互斥
```