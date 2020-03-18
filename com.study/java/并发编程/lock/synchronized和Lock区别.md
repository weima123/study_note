## 一.synchronized和lock(reentrantLock）的区别
### 1.锁的实现
```
1.synchronized是jvm提供的关键字，可用于类、方法、代码块的加锁；
2.reentrantLock是jdk提供用于操作锁的类，只能用于代码块
warn:java每个对象都有一个monitor监视器，synchronized主要通过monitor.enter monitor.exit来进行加锁和锁的释放
````
### 2.锁的释放
```
1.synchronized锁的释放，由jvm控制；发生异常的情况下会自动释放锁 ；调用sleep方法会释放锁
2.reentrantLock锁的释放，通过调用unLock方法来控制；发生异常的情况下如果没有显示调用unLock方法，那么锁不会自动释放
```

### 3.是否获得锁的判断
```
reentrantLock可以判断当前线程是否获得锁,synchronized无法判断当前线程是否获得锁
```

### 3.锁的机制
1)乐观锁、悲观锁
```
synchronized是悲观锁，
reentrantLock是乐观锁，
```
2）公平锁、非公平锁
```
reentrantLock能指定使用公平锁，还是非公平锁，synchronized只能是非公平锁

```
### 4.锁的唤醒
```
1)synchronized只能通过wait notify notifyAll随机唤醒或者全部唤醒锁对象等待池中的线程到锁池中
2）lock可以根据不通的condition选择wait或者notify不同的线程
```
### 4.性能
```
synchronized 性能较reentrantLock差
```
