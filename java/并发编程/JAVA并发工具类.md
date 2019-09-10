[toc]



### JAVA并发工具类


#### CountDownLatch
CountDownLatch允许一个或多个线程等待其他的完成操作，原来要实现主线程等待所有的子线程执行完成最简单的做法就是使用join()方法，代码如下：
```java
    public static void main(String[] args) throws IOException, InterruptedException {
        Thread thread1 = new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.printf("thread1 start.......");
            }
        });
        Thread thread2 = new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.printf("thread2 start.......");
            }
        });
        thread1.start();
        thread2.start();
        thread1.join();
        thread2.join();
        System.out.printf("end.......");
    }
```

join用于当前线程等待join线程执行完成，原理就是不停的检查join线程是否存活，如果线程存活就让当前线程一直等待：

```java
//判断线程是否存活
while (isAlive()) {
	//等待
	wait(0);
}
```

java1.5后JAVA API提供了CountDownLatch，也可以实现join的功能并功能更丰富，CountDownLatch的countDown()方法没执行一次减1，当构造方法传入的数字减为0时await()方法通过。

```java
	static CountDownLatch countDownLatch = new CountDownLatch(2);
    public static void main(String[] args) throws IOException, InterruptedException {
        Thread thread1 = new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.printf("thread1 start.......");
                countDownLatch.countDown();
            }
        });
        Thread thread2 = new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.printf("thread2 start.......");
                countDownLatch.countDown();
            }
        });
        thread1.start();
        thread2.start();
        //countDownLatch.await();
        countDownLatch.await(1000,TimeUnit.MILLISECONDS);
    }
```

#### CyclicBarrier

同步屏障，

```java
static CyclicBarrier cyclicBarrier = new CyclicBarrier(3);
public static void main(String[] args) throws IOException, InterruptedException, BrokenBarrierException {
        Thread thread1 = new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("thread1 start.......");
                try {
                    cyclicBarrier.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            }
        });
        Thread thread2 = new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("thread2 start.......");
                try {
                    cyclicBarrier.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            }
        });
        thread1.start();
        thread2.start();
        cyclicBarrier.await();
    }
```

