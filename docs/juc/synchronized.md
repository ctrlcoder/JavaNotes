## 线程安全

### 模拟卖票过程

```java
public class Ticket implements Runnable {
    private int ticket = 100;
    /*
     * 执行卖票操作
     */
    @Override
    public void run() {
		//每个窗口卖票的操作
		//窗口 永远开启
        while (true) {
            if (ticket > 0) {//有票 可以卖
				//出票操作
				//使用sleep模拟一下出票时间
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
				// TODO Auto‐generated catch block
                    e.printStackTrace();
                }
				//获取当前线程对象的名字
                String name = Thread.currentThread().getName();
                System.out.println(name + "正在卖:" + ticket‐‐);
            }
        }
    }
}


//测试类
public class Demo {
	public static void main(String[] args) {
		//创建线程任务对象
		Ticket ticket = new Ticket();
		//创建三个窗口对象
		Thread t1 = new Thread(ticket, "窗口1");
		Thread t2 = new Thread(ticket, "窗口2");
		Thread t3 = new Thread(ticket, "窗口3");
		//同时卖票
		t1.start();
		t2.start();
		t3.start();
	}
}
```

结果会出现几个窗口票数不同步的问题，这种问题称为线程不安全。

> 线程安全问题都是由全局变量及静态变量引起的。若每个线程中对全局变量、静态变量只有读操作，而无写 
>
> 操作，一般来说，这个全局变量是线程安全的；若有多个线程同时执行写操作，一般都需要考虑线程同步， 
>
> 否则的话就可能影响线程安全。 

## 线程同步

### 同步代码块

**同步代码块**： synchronized 关键字可以用于方法中的某个区块中，表示只对这个区块的资源实行互斥访问。 

```java
synchronized(同步锁){
	需要同步操作的代码
}
```

> 对象的同步锁只是一个概念,可以想象为在对象上标记了一个锁。1. 锁对象 可以是任意类型；2. 多个线程对象要使用同一把锁。 

使用同步代码块解决:

```java
public class Ticket implements Runnable{
    private int ticket = 100;
    Object lock = new Object();
    /*
     * 执行卖票操作
     */
    @Override
    public void run() {
		//每个窗口卖票的操作
		//窗口 永远开启
        while(true) {
            synchronized (lock) {
                if (ticket > 0) {//有票 可以卖
				//出票操作
				//使用sleep模拟一下出票时间
                    try {
                        Thread.sleep(50);
                    } catch (InterruptedException e) {
					// TODO Auto‐generated catch block
                        e.printStackTrace();
                    }
					//获取当前线程对象的名字
                    String name = Thread.currentThread().getName();
                    System.out.println(name + "正在卖:" + ticket‐‐);
                }
            }
        }
    }
}
```

### 同步方法

**同步方法**:使用synchronized修饰的方法,就叫做同步方法,保证A线程执行该方法的时候,其他线程只能在方法外等着。 

```java
public synchronized void method(){
	可能会产生线程安全问题的代码
}
```

> 同步锁是谁? 
>
> 对于非static方法,同步锁就是this。 
>
> 对于static方法,我们使用当前方法所在类的字节码对象(类名.class)。 

```java
public class Ticket implements Runnable {
    private int ticket = 100;

    /*
     * 执行卖票操作
     */
    @Override
    public void run() {
	//每个窗口卖票的操作
	//窗口 永远开启
        while (true) {
            sellTicket();
        }
    }

    /*
     * 锁对象 是 谁调用这个方法 就是谁
     * 隐含 锁对象 就是 this
     *
     */
    public synchronized void sellTicket() {
        if (ticket > 0) {//有票 可以卖
		//出票操作
		//使用sleep模拟一下出票时间
            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
			// TODO Auto‐generated catch block
                e.printStackTrace();
            }
		//获取当前线程对象的名字
            String name = Thread.currentThread().getName();
            System.out.println(name + "正在卖:" + ticket‐‐);
        }
    }
}
```

