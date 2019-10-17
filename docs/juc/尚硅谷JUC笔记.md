> java.util.concurrent
>
> java.util.concurrent.atomic
>
> java.util.concurrent.locks



## Lock接口

### 复习Synchronized

**多线程编程模版**：1.线程操作资源类 2.高内聚低耦合

**实现步骤**：1.创建资源类 2.资源里创建同步方法、同步代码块

## Lock

java.util.concurrent.locks.Lock 机制提供了比**synchronized**代码块和**synchronized**方法更广泛的锁定操作, 

同步代码块/同步方法具有的功能Lock都有,除此之外更强大,更体现面向对象。 

**Lock锁也称同步锁，加锁与释放锁方法化了，如下：** 

public void lock() :加同步锁。 

public void unlock() :释放同步锁。 

```java
public class Ticket {
    private int ticket = 30;
    Lock lock = new ReentrantLock();

    public void sale() {

        lock.lock();

        try {
            if(ticket>0)
                System.out.println(Thread.currentThread().getName()+"正在卖:"+(ticket--));
        } catch (Exception e) {
            e.printStackTrace();
        }finally{
            lock.unlock();
        }

    }

}
```



## FutureTask

一般 FutureTask 多用于耗时的计算，主线程可以在完成自己的任务后，在去获取结果。

只计算一次





## 线程间通信

**模版**

1. 判断
2. 干活
3. 通知

**判断要用while**

虚假唤醒，在java多线程判断时，不能用if

```java
public class ShawData {
    private int number  = 0;

    public synchronized  void  increment() throws InterruptedException{
        //判断
        while(number != 0){
            this.wait();
        }
        //干活
        ++number;
        System.out.println(Thread.currentThread().getName()+" "+number);
        //通知
        this.notifyAll();
    }

    public synchronized  void  decrement() throws InterruptedException{
        //判断
        while(number != 1){
            this.wait();
        }
        //干活
        --number;
        System.out.println(Thread.currentThread().getName()+" "+number);
        //通知
        this.notifyAll();
    }
}
```



### 线程定制化通信



```java
public class ShareResource {
    private int num = 1;
    private Lock lock = new ReentrantLock();
    private Condition c1 = lock.newCondition();
    private  Condition c2 = lock.newCondition();

    public void print5(int loopNum){
        lock.lock();
        try {
            //1 判断
            while(num!=1){
                c1.await();
            }
            for (int i = 1; i <= 5; i++) {
                System.out.println(Thread.currentThread().getName()+" "+i+" "+loopNum);
            }
            num = 2;
            c2.signal();
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }

    public void print10(int loopNum){
        lock.lock();
        try {
            //1 判断
            while(num!=2){
                c2.await();
            }
            for (int i = 1; i <= 10; i++) {
                System.out.println(Thread.currentThread().getName()+" "+i+" "+loopNum);
            }
            num = 1;
            c1.signal();
        }catch (Exception e){
            e.printStackTrace();
        }finally {
            lock.unlock();
        }
    }
}

```

### ReentrantReadWriteLock读写锁

缓存，适用于读多写少的场景。

读写锁遵守以下三条基本原则：

1. 允许多个线程同时读共享变量；
2. 只允许一个线程写共享变量
3. 如果一个写线程正在执行写操作，此时禁止读线程读共享变量。

```java
public class MyQueue {
    private Object object;
    private ReentrantReadWriteLock rwLock = new ReentrantReadWriteLock();

    public  void  readObject(){
        rwLock.readLock().lock();
        try {
            System.out.println(Thread.currentThread().getName()+"\t"+object);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            rwLock.readLock().unlock();
        }
    }

    public  void  writeObject(Object object){
        rwLock.writeLock().lock();
        try {
            this.object=object;
            System.out.println(Thread.currentThread().getName()+"writeThread:\t"+object);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            rwLock.writeLock().unlock();
        }
    }

}

```

读写锁和互斥锁的区别：读写锁允许多个线程同时读共享变量，而互斥锁是不允许的。



### CountDownLatch减少计数

- 当一个或多个线程调用await方法时，这些线程会阻塞
- 其他线程调用方法coutDown方法会将计数器减1
- 当计数器的值变为0，因await方法阻塞的线程会被唤醒，继续执行。

**CountDownLatch主要用来解决一个线程等待多个线程的场景。**

```java
public class CountDownLatchDemo {
    public static void main(String[] args) {
        CountDownLatch countDownLatch = new CountDownLatch(5);

        for (int i = 1; i <= 5 ; i++) {
            new Thread(()->{
                System.out.println(Thread.currentThread().getName()+"号同学离开");
                countDownLatch.countDown();
            },String.valueOf(i)).start();

        }
        try {
            countDownLatch.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread().getName()+"班长关门");
    }
}

```

### CyclicBarrier

让一组线程到达一个屏障（同步点）时被阻塞，直到最后一个线程到达屏障时，屏障才会开门，所有被屏障拦截的线程才会继续干活。线程进入屏障通过CyclicBarrier的await()方法。

CyclicBarrier是一组线程之间相互等待，CyclicBarrier的计数器是可以循环利用的。

```java
public class CyclicBarrierDemo {
    private static final int NUM = 7;

    public static void main(String[] args) {
        CyclicBarrier cyclicBarrier = new CyclicBarrier(NUM,()->{
            System.out.println("集齐7颗龙珠");

        });

        for (int i = 1; i <= 7 ; i++) {
            new Thread(()->{
                try {
                    System.out.println(Thread.currentThread().getName()+"星龙珠被收集");
                    cyclicBarrier.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                } finally {
                }
            },String.valueOf(i)).start();
        }
    }
}

```



### Semaphore信号灯

信号量主要有两个目的

- 用于多个共享资源的互斥使用
- 用于并发线程数的控制

在信号量上我们定义两种操作：

- acquire（获取）：当一个线程调用该方法，它要么成果获取信号量（信号量-1），要么一只等待，知道有线程释放信号量，或超时
- release（释放）：将信号量的值+1，然后唤醒等待的线程

```java
public class SemaphoreDemo {
    public static void main(String[] args) {
        Semaphore semaphore = new Semaphore(3);

        for (int i = 0; i < 6; i++) {
            new Thread(()->{
                try {
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName()+"抢到了车位");
                    TimeUnit.SECONDS.sleep(new Random().nextInt(5));
                    System.out.println(Thread.currentThread().getName()+"离开");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    semaphore.release();
                }

            },String.valueOf(i)).start();
        }
    }

}

```

