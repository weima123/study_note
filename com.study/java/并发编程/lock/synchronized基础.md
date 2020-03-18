## 一.使用
#### synchronized关键字有如下两种用法：

#### 1、 在需要同步的方法的方法签名中加入synchronized关键字。
````
synchronized public void getValue() {
    System.out.println("getValue method thread name="
            + Thread.currentThread().getName() + " username=" + username
            + " password=" + password);
}
````
####上面的代码修饰的synchronized是非静态方法，如果修饰的是静态方法（static）含义是完全不一样的。
````
synchronized static public void getValue() {
    System.out.println("getValue method thread name="
            + Thread.currentThread().getName() + " username=" + username
            + " password=" + password);
}
````
#### 2、使用synchronized块对需要进行同步的代码段进行同步。
````
public void serviceMethod() {
    try {
        synchronized (this) {
            System.out.println("begin time=" + System.currentTimeMillis());
            Thread.sleep(2000);
            System.out.println("end    end=" + System.currentTimeMillis());
        }
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}
````
#### 上面的代码块是synchronized (this)用法，还有synchronized (非this对象)以及synchronized (类.class)这两种用法，这些使用方式的含义也是有根本的区别的。我们先带着这些问题继续往下看。

## 二.作用
### synchronized关键字是java内线程同步的关键字，主要目的是在多线程环境下，保证方法或者方法块内的代码在同一时刻只有一个线程在执行
## 三.源码
````
synchronized是关键字，不存在源码
````
## 四.示例
#### 示例一
````
public class PublicMethod {
    private int num;

    public PublicMethod(int num) {
        this.num = num;
    }

    public PublicMethod() {
    }

    public int getNum() {
        return num;
    }



    public void setNum(int num) {
        this.num = num;
    }

    public  void add(){
        String threadName = Thread.currentThread().getName();

        if ("a_thread".equalsIgnoreCase(threadName)){
            num = 100;
            System.out.println("a_thread set num over ");
            try {
                Thread.sleep(6000L);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }else {
            num = 200;
            System.out.println("b_thread set num over ");
        }

        System.out.println(threadName + " num ="+num);
    }
}

 
public class AThread extends Thread {

    private PublicMethod publicMethod;

    public AThread(PublicMethod publicMethod){
        super("a_thread");
        this.publicMethod = publicMethod;
    }

    @Override
    public void run(){
        this.publicMethod.add();
    }
}

 
public class BThread extends Thread{
    private PublicMethod publicMethod;

    public BThread(PublicMethod publicMethod){
        super("b_thread");
        this.publicMethod = publicMethod;
    }

    @Override
    public void run(){
        publicMethod.add();
    }
}
 
 public class Main01 {
     public static void main(String []args){
         PublicMethod publicMethod = new PublicMethod();
         AThread aThread = new AThread(publicMethod);
         BThread bThread = new BThread(publicMethod);
         aThread.start();
         bThread.start();
     }
 }

````
log
````
a set over!
b set over!
b num=200
a num=200
````
#### 修改PublicMethod如下，方法用synchronized修饰如下：
````
public class PublicMethod {
    private int num;

    public PublicMethod(int num) {
        this.num = num;
    }

    public PublicMethod() {
    }

    public int getNum() {
        return num;
    }



    public void setNum(int num) {
        this.num = num;
    }

    public synchronized void add(){
        String threadName = Thread.currentThread().getName();

        if ("a_thread".equalsIgnoreCase(threadName)){
            num = 100;
            System.out.println("a_thread set num over ");
            try {
                Thread.sleep(6000L);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }else {
            num = 200;
            System.out.println("b_thread set num over ");
        }

        System.out.println(threadName + " num ="+num);
    }
}

````
log --运行结果是线程安全的
````
a_thread set num over 
a_thread num =100
b_thread set num over 
b_thread num =200

````
### 实验结论：两个线程访问同一个对象中的同步方法是一定是线程安全的。本实现由于是同步访问，所以先打印出a，然后打印出b

## 示例二 （多个对象多个锁）
````
public class Main02 {

    public static void main(String []args){
    PublicMethod publicMethod01 = new PublicMethod();
    PublicMethod publicMethod02 = new PublicMethod();
    AThread aThread = new AThread(publicMethod01);
    aThread.start();
    BThread bThread = new BThread(publicMethod02);
    bThread.start();
    }
}
````
#### log
````
a_thread set num over 
b_thread set num over 
b_thread num =200
a_thread num =100
````
#### 实验结论：这里线程A和线程B是非线程同步的，A线程获得的是publicMethod01的对象锁，B线程获得的是publicMethod02的对象锁，他们在获取锁上不存在竞争关系，因此A线程 B线程会出现非同步的情况

## 示例三 （同步块synchronized (this))
#### 修改PublicMethod如下
````
public class PublicMethod {
    private int num;

    public PublicMethod(int num) {
        this.num = num;
    }

    public PublicMethod() {
    }

    public int getNum() {
        return num;
    }


    public void setNum(int num) {
        this.num = num;
    }

    public void add() {
        synchronized (this) {
            String threadName = Thread.currentThread().getName();
            if ("a_thread".equalsIgnoreCase(threadName)) {
                num = 100;
                System.out.println("a_thread set num over ");
                try {
                    Thread.sleep(6000L);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            } else {
                num = 200;
                System.out.println("b_thread set num over ");
            }

            System.out.println(threadName + " num =" + num);
        }
    }
}

 public class Main01 {
     public static void main(String []args){
         PublicMethod publicMethod = new PublicMethod();
         AThread aThread = new AThread(publicMethod);
         BThread bThread = new BThread(publicMethod);
         aThread.start();
         bThread.start();
     }
 }
````
#### log
````
a_thread set num over 
a_thread num =100
b_thread set num over 
b_thread num =200
````
#### 实验总结：如上这样也是同步的，线程获取了synchornized(this)括号里对象示例的对象锁，这里就是PublicMethod对象实例publicMethod的锁。
#### 注意:synchornized(this){}同步块之外的代码还是非同步的


## 示例四(synchornized(obj){})
#### 1.未加锁的情况
````
public class Thread03 extends Thread {
    private String lock;

    public Thread03(String lock, String threadName) {
        super(threadName);
        this.lock = lock;
    }

    @Override
    public void run() {
        String threadName = Thread.currentThread().getName();

        try {
            System.out.println(threadName + " 进入同步块代码");
            Thread.sleep(6000);
            System.out.println(threadName + " 离开同步块代码");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

    }
}

public class Main03 {
    public static void main(String[] args) {
        String lock = new String("345");
        Thread03 a_thread = new Thread03(lock,"a_thread");
        a_thread.start();
        Thread03 b_thread = new Thread03(lock,"b_thread");
        b_thread.start();
    }
}

````
#### log
````
a_thread 进入同步块代码
b_thread 进入同步块代码
b_thread 离开同步块代码
a_thread 离开同步块代码
````
#### 结果分析：在未加同步锁的情况下，线程A、B是异步的

#### 2.加锁的情况
````
public class Thread03 extends Thread {
    private String lock;

    public Thread03(String lock, String threadName) {
        super(threadName);
        this.lock = lock;
    }

    @Override
    public void run() {
        String threadName = Thread.currentThread().getName();
        synchronized (lock) {
            try {
                System.out.println(threadName + " 进入同步块代码");
                Thread.sleep(6000);
                System.out.println(threadName + " 离开同步块代码");
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

````
#### log
````
a_thread 进入同步块代码
a_thread 离开同步块代码
b_thread 进入同步块代码
b_thread 离开同步块代码
````

#### 结果分析:加锁的情况下，线程a、b是同步的，不难看出这里a.b线程争抢的是lock对象的对象锁，两个线程有竞争同一对象锁的关系，出现同步

#### 总结：对于对象A，如果线程a获得了A的对象锁，那么其他线程就不能进入需要获取A的对象锁才能访问的同步代码（同步方法和同步块）中。

## 示例五  静态synchronized同步方法

````
//未加锁的情况
public class PublicMethod04 {
     public static void printA(){
        String threadName = Thread.currentThread().getName();
        System.out.println(threadName+" 进入printA");
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(threadName + " 离开printA");
    }

     public static void printB(){
        String threadName = Thread.currentThread().getName();
        System.out.println(threadName + " 进入printB");
        System.out.println(threadName+" 离开printB");
    }
}
public class Thread04A extends Thread {
    public Thread04A() {
        super("a_thread");
    }

    @Override
    public void run() {
        PublicMethod04.printB();
    }
}
public class Thread04B extends Thread {

    public Thread04B() {
        super("b_thread");
    }

    @Override
    public void run() {
        super.run();
        PublicMethod04.printB();
    }
}

public class Main04 {
    public static void main(String []args){
         Thread04A thread04A = new Thread04A();
         Thread04B thread04B = new Thread04B();
         thread04A.start();
         thread04B.start();
    }
}
````
#### log
````
b_thread 进入printB
a_thread 进入printB
b_thread 离开printB
a_thread 离开printB
````
````
//加锁的情况
public class PublicMethod04 {
    synchronized public static void printA(){
        String threadName = Thread.currentThread().getName();
        System.out.println(threadName+" 进入printA");
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(threadName + " 离开printA");
    }

    synchronized public static void printB(){
        String threadName = Thread.currentThread().getName();
        System.out.println(threadName + " 进入printB");
        System.out.println(threadName+" 离开printB");
    }
}

````
##### log

````
a_thread 进入printB
a_thread 离开printB
b_thread 进入printB
b_thread 离开printB
````
#### 分析:加锁情况下，对于静态类线程A、B对静态类的类锁存在竞争关系，因此线程同步


## 示例六（synchronized(class))
````
//对示例五中的代码做如下变更
public class PublicMethod04 {
    public static void printA(){
        synchronized (PublicMethod04.class) {
            String threadName = Thread.currentThread().getName();
            System.out.println(threadName + " 进入printA");
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(threadName + " 离开printA");
        }
    }

    public static void printB(){
        synchronized (PublicMethod04.class) {
            String threadName = Thread.currentThread().getName();
            System.out.println(threadName + " 进入printB");
            System.out.println(threadName + " 离开printB");
        }
    }
}

````
#### log

````
a_thread 进入printB
a_thread 离开printB
b_thread 进入printB
b_thread 离开printB
````

#### 结论:加锁情况下，对于静态类线程A、B对静态类的类锁存在竞争关系，因此线程还是同步(对于synchronized(Class)静态方法，非静态方法都是一样的情况)

#### 需要特别说明：对于同一个类A，线程1争夺A对象实例的对象锁，线程2争夺类A的类锁，这两者不存在竞争关系。也就说对象锁和类锁互补干预内政

#### 静态方法则一定会同步，非静态方法需在单例模式才生效，但是也不能都用静态同步方法，总之用得不好可能会给性能带来极大的影响。另外，有必要说一下的是Spring的bean默认是单例的。
#### 静态方法中使用synchronized 、synchronized(class) 线程间竞争的是类锁，使用synchronized(obj)线程间竞争的是对象锁
#### 非静态方法直接使用synchronized 、synchronized(this) 、synchronized(obj)线程间竞争的是对象锁,使用synchronized(class)竞争的是类锁









