---
​---
Title:启动两个线程, 一个输出 1,3,5,7…99, 另一个输出 2,4,6,8…100 最后 STDOUT 中按序输出 1,2,3,4,5…100
date:2019-04-07 12:34:12
​---

---

Java可以用notify，wait来实现。

首先我们写两个线程一个输出奇数，一个输出偶数

```java
class ThreadOdd extends Thread {
    @Override
    public void run() {
        for (int i = 1; i < 100; i += 2) {
            System.out.println(i);
        }
    }
}
```
```java
class ThreadEven extends Thread {
    @Override
    public void run() {
        for (int i = 2; i <= 100; i += 2) {
            System.out.println(i);
        }
    }
}
```

建立一个锁

```java
private final Object lock = new Object();
```

因为奇数需要优先输出，所以奇数先打印，再唤醒偶数线程，最后进入等待状态。

```java
  class ThreadOdd extends Thread {
        @Override
        public void run() {
            synchronized (lock) {
                for (int i = 1; i < 100; i += 2) {
                    System.out.println(i);
                    lock.notify();
                    try {
                        lock.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        }
    }
```

偶数先wait，然后唤醒奇数，最后进入等待状态

```java
class ThreadEven extends Thread {
        @Override
        public void run() {
            synchronized (lock) {
                for (int i = 2; i <= 100; i += 2) {
                    try {
                        lock.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println(i);
                    lock.notify();
                }
          }
      }
}
```

我们尝试运行一下

```java
public static void main(String[] args) throws Exception {
        Notify notify = new Notify();
        notify.new ThreadOdd().start();
        notify.new ThreadEven().start();
    }
```

当我们先执行奇数线程时，结果只输出了1一个数

**原因是因为奇数线程在notify之后，偶数线程还并未进入wait状态**

所以我们需要偶数线程进入wait状态之后，奇数线程再notify，可以通过一个变量来解决

```java
private boolean isWait = false;
```

偶数线程在等待之前把isWait变量设置成true

```java
    class ThreadEven extends Thread {
        @Override
        public void run() {
            synchronized (lock) {
                for (int i = 2; i <= 100; i += 2) {
                    try {
                        isWait = true;
                        lock.notify();
                        lock.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println(i);
                    lock.notify();
                }
            }
        }
    }
```

奇数线程在isWait是fale的时候一直等待

```java
  class ThreadOdd extends Thread {
        @Override
        public void run() {
            synchronized (lock) {
                while (!isWait) {
                    try {
                        lock.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                for (int i = 1; i < 100; i += 2) {
                    System.out.println(i);
                    lock.notify();
                    try {
                        lock.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        }
    }
```

这样就可以正确输出了



**完整代码**

```java
package hanyijun.algorithm;


/**
 * 启动两个线程, 一个输出 1,3,5,7…99, 另一个输出 2,4,6,8…100 最后 STDOUT 中按序输出 1,2,3,4,5…100
 */
public class Notify {
    private final Object lock = new Object();
    private boolean isWait = false;

    public static void main(String[] args) throws Exception {
        Notify notify = new Notify();
        notify.new ThreadOdd().start();
        notify.new ThreadEven().start();
    }

    class ThreadEven extends Thread {
        @Override
        public void run() {
            synchronized (lock) {
                for (int i = 2; i <= 100; i += 2) {
                    try {
                        isWait = true;
                        lock.notify();
                        lock.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println(i);
                    lock.notify();
                }
            }
        }
    }

    class ThreadOdd extends Thread {
        @Override
        public void run() {
            synchronized (lock) {
                while (!isWait) {
                    try {
                        lock.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                for (int i = 1; i < 100; i += 2) {
                    System.out.println(i);
                    lock.notify();
                    try {
                        lock.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        }
    }
}
```



