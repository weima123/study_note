## 一.使用方式
###  Thread.join()是java.Lang.Thread提供的一个方法，具体调用如下:
````
Thread thread = new Thread();     
thread.start();      
thread.join();      
````
## 二.为什么要使用join
### 很多情况下，主线程main生成了子线程thread，子线程thread中可能涉及到大量的逻辑运算，耗时教久，因此主线程main会先于子线thread程结束，在主线程main可能用到子线程thread执行结果的情况下，使用thread.join()方法，让主线程在thread.join()方法后的代码，在等待子线程thread结束后再执行。

## 三.join方法的作用
#### 在JDk的API里对于join()方法是：
````
join   
public final void join() throws InterruptedException Waits for this thread to die. Throws: InterruptedException  - if any thread has interrupted the current thread. The interrupted status of the current thread is cleared when this exception
 is thrown
````
####即join()的作用是：“等待该线程终止”，这里需要理解的就是该线程是指的主线程等待子线程的终止。也就是在子线程调用了join()方法后面的代码，只有等到子线程结束了才能执行.
####t.join()方法阻塞调用此方法的线程(calling thread)，直到线程t完成，此线程再继续；通常用于在main()主线程内，等待其它线程完成再结束main()主线程
## 四.join的实际使用
````
public class ThreadA extends Thread {
    public ThreadA() {
        super("[ThreadA]");
    }

    @Override
    public void run() {
        String threadName = Thread.currentThread().getName();
        System.out.println(threadName+"thread start ..");
        try {
            Thread.sleep(1000L);
        } catch (InterruptedException e) {
            e.printStackTrace();
            System.out.println(threadName+"thread exception..");
        }
        System.out.println(threadName + " end ..");
    }
}
````

````
public class ThreadB extends Thread {

    ThreadA threadA;
    public ThreadB(ThreadA threadA){
        super("[ThreadB]");
        this.threadA = threadA;
    }

    @Override
    public void run() {
        String threadName = Thread.currentThread().getName();
        System.out.println(threadName+"start ..");
        try {
            threadA.join();
            Thread.sleep(1000L);
        } catch (InterruptedException e) {
            e.printStackTrace();
            System.out.println(threadName+"exception ..");
        }
        System.out.println(threadName+"end ..");
    }
}
````
#### 案例一:
````
public class MainThread {
    public static void main(String []args){
        String threadName = Thread.currentThread().getName();
        System.out.println(threadName+" start..");
        ThreadA threadA = new ThreadA();
        ThreadB threadB = new ThreadB(threadA);
        threadB.start();
        threadA.start();
        System.out.println(threadName+" end ..");

    }
}
````
#### log
````
main start.. //主线程启动
main end .. //主线程结束
[ThreadB]start .. //B线程启动
[ThreadA]thread start .. //A线程启动
[ThreadA] end .. //A线程结束
[ThreadB]end .. //B线程结束
分析:如上所示,1.B线程总会等到A线程结束后再结束,
2.主线程不会因为b线程内调用了threadA.join()而等待A线程结束后再结束
````

#### 案例二
````
public class MainThread {
    public static void main(String []args){
        String threadName = Thread.currentThread().getName();
        System.out.println(threadName+" start..");
        ThreadA threadA = new ThreadA();
        ThreadB threadB = new ThreadB(threadA);
        threadB.start();
        threadA.start();
        try {
            threadA.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
            System.out.println(threadName+" exception ..");
        }
        try {
            threadB.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
            System.out.println(threadName+" exception ..");
        }
        System.out.println(threadName+" end ..");

    }
}
````
#### log
````
main start..
[ThreadB]start ..
[ThreadA]thread start ..
[ThreadA] end ..
[ThreadB]end ..
main end ..
分析:1.主线程总是会等待A、B线程结束后再结束,
2.B线程总是等待A线程结束后结束
````

#### 案例三
````
public class MainThread {
    public static void main(String []args){
        String threadName = Thread.currentThread().getName();
        System.out.println(threadName+" start..");
        ThreadA threadA = new ThreadA();
        ThreadB threadB = new ThreadB(threadA);
        threadB.start();
        threadA.start();
        try {
            threadA.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
            System.out.println(threadName+" exception ..");
        }
        System.out.println(threadName+" end ..");

    }
}

````
#### log
````
main start..
[ThreadB]start ..
[ThreadA]thread start ..
[ThreadA] end ..
main end ..
[ThreadB]end ..
分析:
1.主线程总是等待A线程结束后结束
2.B线程总是等待A线程结束后结束
````
## 五.源码分析
````
    public final synchronized void join(long millis)
    throws InterruptedException {
        long base = System.currentTimeMillis();
        long now = 0;

        if (millis < 0) {
            throw new IllegalArgumentException("timeout value is negative");
        }

        if (millis == 0) {
            while (isAlive()) {//判断当前线程是否处于活动状态
                wait(0);
            }
        } else {
            while (isAlive()) {
                long delay = millis - now;
                if (delay <= 0) {
                    break;
                }
                wait(delay);
                now = System.currentTimeMillis() - base;
            }
        }
    }
````
从上面的源码可以看出，join()方法主要是通过wait()方法来实现的。
