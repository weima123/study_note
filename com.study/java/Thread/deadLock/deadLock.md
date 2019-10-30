## 一.死锁产生的原因分析
#### 1.死锁产生的原因：
````
死锁的产生是由于多个线程，因资源争夺而导致的相互等待的现象，若无外力介入，那么他们都将一直等待下去
````

## 二.如何实现一个死锁
````
public class DeadLockRunnable02 implements Runnable {
    private Object object01 ;
    private Object object02 ;

    private String tName ;

    public DeadLockRunnable02(Object object01, Object object02) {
        this.object01 = object01;
        this.object02 = object02;
    }

    @Override
    public void run(){
        tName = Thread.currentThread().getName();
        synchronized (object01){
            System.out.println(tName + "lock"+ object01);
            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            synchronized (object02){
                System.out.println(tName + "lock" + object02);
            }
            System.out.println(tName + "release-lock" + object02);
        }
        System.out.println(tName + "release-lock" + object01);
    }
    
    
    
    public static void main(String []args){
      DeadLockRunnable deadLockRunnable01 = new DeadLockRunnable(true);
      DeadLockRunnable deadLockRunnable02 = new DeadLockRunnable(false);

      Thread thread01 = new Thread(deadLockRunnable01);
      thread01.setName("thread01");
      Thread thread02 = new Thread(deadLockRunnable02);
      thread02.setName("thread02");
      thread01.start();
      thread02.start();
    }
}


````

## 三.如何预防死锁
### 1.增加获取锁的顺序
````
1）避免锁嵌套
````
### 2.增加锁超时时间
````
1）在线程尝试获取锁的时候增加一个超时时间，当线程在超时时间内一直未获得锁时，线程自动回退并释放所有的已获得锁。
````

### 3.死锁检测
````
1）对线程每一次获取锁、释放锁的操作进行监控，方便分析死锁原因
````