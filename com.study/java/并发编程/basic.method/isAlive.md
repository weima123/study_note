## 一.使用
````
Thread thread = new Thread();
thread.isAlive()
````
## 二.作用
#### 1.isAlive()用来判断当前线程是否处于活动状态(准备运行或者运行中)

## 三.源码分析
````
public final native boolean isAlive();
````
#### native修饰的方法是一个原生态方法，说明方法的实现不在当前文件中，而在其他文件（c、c++）中，java本身无法进行操作系统底层的访问，但可以通过JNI接口调用其他语言来进行访问。
## 四.代码示例
````
public class AThread extends Thread {

    public AThread() {
        super("[AThread]");
    }

    @Override
    public void run() {
        String threadName = Thread.currentThread().getName();
        System.out.println(threadName+"isAlive:"+Thread.currentThread().isAlive());
    }
}
````
````
public class MainThread {
    public static void main(String[] args) {
        AThread aThread = new AThread();
        aThread.start();
        try {
            aThread.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(aThread.getName()+"isAlive:"+aThread.isAlive());
    }
}
````

log
````
[AThread]isAlive:true
[AThread]isAlive:false
````
