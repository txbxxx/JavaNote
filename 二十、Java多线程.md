:book: 青空的B站课程

:date: 2024年4月25日



> ps: 这节课需要回顾一下操作系统的知识
>

### 多线程



#### 多线程的创建和启动

:star: 创建多线程可以通过`Tread`来创建，而`Tread`方法需要传入一个`Runnable`的接口实现(使用内部类)

:one: `Tread`的构建方法

```java
    /**
     * Allocates a new {@code Thread} object. This constructor has the same
     * effect as {@linkplain #Thread(ThreadGroup,Runnable,String) Thread}
     * {@code (null, target, gname)}, where {@code gname} is a newly generated
     * name. Automatically generated names are of the form
     * {@code "Thread-"+}<i>n</i>, where <i>n</i> is an integer.
     *
     * @param  target
     *         the object whose {@code run} method is invoked when this thread
     *         is started. If {@code null}, this classes {@code run} method does
     *         nothing.
     */
    public Thread(Runnable target) {
        init(null, target, "Thread-" + nextThreadNum(), 0);
    }
```

:two: `Runnable`在线程中是一个需要去实现的方法，就是实现就是线程需要执行的操作

```java
@FunctionalInterface
public interface Runnable {
    /**
     * When an object implementing interface <code>Runnable</code> is used
     * to create a thread, starting the thread causes the object's
     * <code>run</code> method to be called in that separately executing
     * thread.
     * <p>
     * The general contract of the method <code>run</code> is that it may
     * take any action whatsoever.
     *
     * @see     java.lang.Thread#run()
     */
    public abstract void run(); //通过实现run()方法来实现线程的操作
}
```

:three: 演示执行计算1-100的和，这只是创建一个线程，规定线程的作用，但是并没有开始运行线程

```java
    Thread thread = new Thread(new Runnable() { //这是一个匿名内部类
        @Override
        public void run() {
            int sum = 0;
            for (int i = 1; i <= 100; i++) {
                sum += i;
            }
            System.out.println(sum);
        }
    });
    }


//替换成lambda
    public static void main(String[] args) {
    Thread thread = new Thread(() -> {
        int sum = 0;
        for (int i = 1; i <= 100; i++) {
            sum += i;
        }
        System.out.println(sum);
    });
    }
```

:four: 使用`start`方法开始执行`thread`

```java
    public static void main(String[] args) {
    Thread thread = new Thread(() -> {
        int sum = 0;
        for (int i = 1; i <= 100; i++) {
            sum += i;
        }
        System.out.println(sum);
    });
    thread.start();
    }

//输出: 5050
```

:five: 主线程和子线程之间演示，主线程和子线程一起在计算，并不是按照顺序来执行的，互补干扰

```java
    public static void main(String[] args) {
        Thread t = new Thread(() -> {
            System.out.println("我是线程："+Thread.currentThread().getName());
            System.out.println("A我正在计算 0-10000 之间所有数的和...");
            int sum = 0;
            for (int i = 0; i <= 10000; i++) {
                sum += i;
            }
            System.out.println("A结果："+sum);
        });
        t.start();
        System.out.println("我是主线程！");
        System.out.println("B我正在计算 0-10000 之间所有数的和...");
        int sum = 0;
        for (int i = 0; i <= 10000; i++) {
            sum += i;
        }
        System.out.println("B结果："+sum);
    }


//输出:
我是主线程！
B我正在计算 0-10000 之间所有数的和...
我是线程：Thread-0
A我正在计算 0-10000 之间所有数的和...
B结果：50005000
A结果：50005000
```

:six: 还有一个案例

```java
public static void main(String[] args) {
    Thread t1 = new Thread(() -> {
        for (int i = 0; i < 50; i++) {
            System.out.println("我是一号线程："+i);
        }
    });
    Thread t2 = new Thread(() -> {
        for (int i = 0; i < 50; i++) {
            System.out.println("我是二号线程："+i);
        }
    });
    t1.start();
    t2.start();
}
```

:star: 除了第一个构建方法外，还有一些构建方法，比如这个在传入一个参数给线程自定义名字的

```java
    /**
     * Allocates a new {@code Thread} object. This constructor has the same
     * effect as {@linkplain #Thread(ThreadGroup,Runnable,String) Thread}
     * {@code (null, target, name)}.
     *
     * @param  target
     *         the object whose {@code run} method is invoked when this thread
     *         is started. If {@code null}, this thread's run method is invoked.
     *
     * @param  name
     *         the name of the new thread
     */
    public Thread(Runnable target, String name) {
        init(null, target, name, 0);
    }
```

:one: 代码演示，在`Runnable`后面在传入一个参数改名字

```java
    public static void main(String[] args) {
        Thread t = new Thread(() -> {
            System.out.println("我是线程："+Thread.currentThread().getName()); //Thread.currentThread后面会介绍
            System.out.println("我正在计算 0-10000 之间所有数的和...");
            int sum = 0;
            for (int i = 0; i <= 10000; i++) {
                sum += i;
            }
            System.out.println("结果："+sum);
        },"子线程A");
        t.start();
        System.out.println("我是主线程！");
    }

//输出:
我是主线程！
我是线程：子线程A //输出名字改了
我正在计算 0-10000 之间所有数的和...
结果：50005000
```

:two: 如果不写名字默认有方法自动改名字

```java
    /**
     * Allocates a new {@code Thread} object. This constructor has the same
     * effect as {@linkplain #Thread(ThreadGroup,Runnable,String) Thread}
     * {@code (group, target, gname)} ,where {@code gname} is a newly generated
     * name. Automatically generated names are of the form
     * {@code "Thread-"+}<i>n</i>, where <i>n</i> is an integer.
     *
     * @param  group
     *         the thread group. If {@code null} and there is a security
     *         manager, the group is determined by {@linkplain
     *         SecurityManager#getThreadGroup SecurityManager.getThreadGroup()}.
     *         If there is not a security manager or {@code
     *         SecurityManager.getThreadGroup()} returns {@code null}, the group
     *         is set to the current thread's thread group.
     *
     * @param  target
     *         the object whose {@code run} method is invoked when this thread
     *         is started. If {@code null}, this thread's run method is invoked.
     *
     * @throws  SecurityException
     *          if the current thread cannot create a thread in the specified
     *          thread group
     */
    public Thread(ThreadGroup group, Runnable target) {
        init(group, target, "Thread-" + nextThreadNum(), 0);
    }
```

:star: 执行方法还有一个`run`，`run`它是只在当前线程去执行，并不是单开一个线程，

:one:  他是直接调用传入的`Runnable`

```java
//实现代码   
	@Override
    public void run() {
        if (target != null) {
            target.run();
        }
    }
```

:two:下列代码实例会发现是运行完t1在运行t2

```java
    public static void main(String[] args) {
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 50; i++) {
                System.out.println("我是一号线程："+i);
            }
        });
        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 50; i++) {
                System.out.println("我是二号线程："+i);
            }
        });
        t1.run();
        t2.run();
    }

//会发现是运行完t1在运行t2
```

:star: 进程会有很多运行状态：等待，运行阻塞

1. **就绪态（ready）**：一个进程已经具备运行条件，但由于某些事情从而不能运行的状态，当他调度给它占用CPU时，立即可以运行。一个进程获得除处理机之外的一切所需资源时，它就会位于“就绪队列中”
2. **执行态（Running state）**：进程占有了包括CPU在内的全部资源，正在CPU上运行；再单机的环境下，每一时刻最多只有一个进程处于运行状态
3. **等待态**：也叫阻塞态，指因等待某种事件发生而暂停运行的状态；会位于等待队列中

![image-20240425154029703](https://image-1305907375.cos.ap-chengdu.myqcloud.com/Flower_bound_manage_boundimage-20240425154029703.png)

:one: 进程切换

1. **运行**➡**就绪**：

2. 1. 运行进程用完了时间片，不得不让出来（被动）
   2. 运行进程被更高优先级的进程中断，所以当前进程被迫处于就绪状态

3. **运行**➡**阻塞**：进程用"系统调用"的方式申请某种操作系统资源，或者请求等待某个事件发生

4. **阻塞**➡**就绪**：当进程所等待的事件发生时，就会进入就绪队列，重新等待处理机的调度

   

:star: 使用`Thread.currentThread`来获取当进程前对象，在进程内部

:one: 获取`Main`线程对象

```java
    public static void main(String[] args) {
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 50; i++) {
                System.out.println("我是一号线程："+i);
                //获取当前进程对象
                Thread thread = Thread.currentThread();
                System.out.println(thread.getName());
            }
        });
        //获取main进程对象
        Thread main = Thread.currentThread();
        System.out.println(main.getName());
    }
```

:star: 可以用`stop`来终止进程，会发现stop，打了一个@`Deprecated`注解，这个要么是弃用，要么是要被移除

```java
@Deprecated
    public final void stop() {
        SecurityManager security = System.getSecurityManager();
        if (security != null) {
            checkAccess();
            if (this != Thread.currentThread()) {
                security.checkPermission(SecurityConstants.STOP_THREAD_PERMISSION);
            }
        }
        // A zero status value corresponds to "NEW", it can't change to
        // not-NEW because we hold the lock.
        if (threadStatus != 0) {
            resume(); // Wake up thread if it was suspended; no-op otherwise
        }

        // The VM can handle all thread states
        stop0(new ThreadDeath());
    }
```

:one: 关门`main`方法(自己搞着玩的)

```java
    public static void main(String[] args) {
        //获取main进程对象
        Thread main = Thread.currentThread();
        main.stop();
        System.out.println(main.getName());

    }
```

:two: 关闭线程

```java
    public static void main(String[] args) {
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 50; i++) {
                System.out.println("我是一号线程："+i);
                //获取当前进程对象
                Thread thread = Thread.currentThread();
                if(i==50) thread.stop();
            }
        });
        t1.start();
    }
```



#### 线程的休眠和中断

![](https://image-1305907375.cos.ap-chengdu.myqcloud.com/Flower_bound_manage_boundimage-20240425154029703.png)

:star: 我们前面提到，一个线程处于运行状态下，线程的下一个状态会出现以下情况：

- 当CPU给予的运行时间结束时，会从运行状态回到就绪（可运行）状态，等待下一次获得CPU资源。
- 当线程进入休眠 / 阻塞(如等待IO请求) / 手动调用`wait()`方法时，会使得线程处于等待状态，当等待状态结束后会回到就绪状态。
- 当线程出现异常或错误 / 被`stop()` 方法强行停止 / 所有代码执行结束时，会使得线程的运行终止。



:star: 可以使用`sleep`方法让程序进入阻塞状态

:one:代码实列

```java
    public static void main(String[] args) throws InterruptedException {
        System.out.print("H");
        Thread.sleep(1000); //停止1秒
        System.out.print("e");
        Thread.sleep(1000);
        System.out.print("l");
        Thread.sleep(1000);
        System.out.print("l");
        Thread.sleep(1000);
        System.out.println("o");
    }
```

:star: 在使用`sleep`的时候会抛出一个`InterruptException`的异常，这个异常为中断异常，当支持中断操作的方法，都会抛出中断异常，这个异常可以在调用`interrupt`方法来终止支持中断异常的方法来发生，`interrupt`它不会和`stop`一样直接强制终止，它会给指定线程添加一个中断标记以告知线程需要立即停止运行或是进行其他操作，由线程来响应此中断并进行相应的处理，也就是告诉你你要中断了，让线程先去处理中断前的操作

```java
//操作 
public static native void sleep(long millis) throws InterruptedException; //支持中断操作
```

> ps: 为什么不推荐使用stop方法呢？因为它是将程序强制直接终止的，之前学过流的概念，在操作完成一个文件后，需要使用close关闭，你直接stop了就表示直接终止进程，各种后事都不会执行，就会导致这个文件一直被占用，是一件很危险的事情

```java
public static void main(String[] args) {
    Thread t = new Thread(() -> {
        try {
            Thread.sleep(10000);  //休眠10秒
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    });
    t.start();
    try {
        Thread.sleep(3000);   //休眠3秒，一定比线程t先醒来
        t.interrupt();   //调用t的interrupt方法
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}
```

这里会抛出一个中断异常

:one: 使用`isInterrupted()`，方法来判断进程是否被打上了中断标记，然后在进行结束前的前置操作

```java
    public static void main(String[] args) {
        Thread t = new Thread(() -> {
            System.out.println("线程开始运行！");
            while (true){   //无限循环
                if(Thread.currentThread().isInterrupted()){   //判断是否存在中断标志
                    System.out.println("我被打了中断标记需要立即停止");
                    break;   //响应中断
                }
            }
            System.out.println("线程被中断了！");
        });
        t.start();
        try {
            Thread.sleep(3000);   //休眠3秒，一定比线程t先醒来
            t.interrupt();   //调用t的interrupt方法
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
```

:star: 如果只是想让进程在接受到一个中断标记之后，**通知进程**切换到进程内另外一个操作也可以使用`interrupt`方法判断，不过需要搭配`interrupted`来重置中断标记

```java
import java.util.TreeMap;

public class Main {

    public static void main(String[] args) {
        Thread t = new Thread(() -> {
            System.out.println("线程开始运行！");
            while (true){   //无限循环
                if(Thread.currentThread().isInterrupted()){   //判断是否存在中断标志
                    System.out.println("我被打了中断标记需要切换到下一个循环");
                    break;   //响应中断
                }
            }
            Thread.interrupted(); //重置中断标记
            while (true){   //无限循环
                if(Thread.currentThread().isInterrupted()){   //判断是否存在中断标志
                    System.out.println("我被打了中断标记需要立即停止！！");
                    break;   //响应中断
                }
            }
            System.out.println("线程被中断了！");
        });
        t.start();
        try {
            Thread.sleep(3000);   //休眠3秒，一定比线程t先醒来
            t.interrupt();   //调用t的interrupt方法
            Thread.sleep(3000);    //让t进程第一个循环先执行
            t.interrupt();   //再次打上中断标记
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

//输出:
线程开始运行！
我被打了中断标记需要切换到下一个循环
我被打了中断标记需要立即停止！！
线程被中断了！

```

:star: 让线程暂停`suspend`，让线程恢复`rusume`

:one: 暂停进程，会发现进程会一直卡住

```java
    public static void main(String[] args) {
        Thread t = new Thread(() -> {
            System.out.println("线程开始运行！");
            Thread.currentThread().suspend(); //暂停此线程
            System.out.println("线程被继续运行！");
        });
        t.start();
        try {
            Thread.sleep(3000);   //休眠3秒，一定比线程t先醒来
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
```

:two: 只有当其他进程使用`resum`方法通知他可以继续运行它才可以运行

```java
    public static void main(String[] args) {
        Thread t = new Thread(() -> {
            System.out.println("线程开始运行！");
            Thread.currentThread().suspend(); //暂停此线程
            System.out.println("线程被继续运行！");
        });
        t.start();
        try {
            Thread.sleep(3000);   //休眠3秒，一定比线程t先醒来
            t.resume();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
```





#### 线程的优先级

实际上，Java程序中的每个线程并不是平均分配CPU时间的，为了使得线程资源分配更加合理，Java采用的是抢占式调度方式，优先级越高的线程，优先使用CPU资源！我们希望CPU花费更多的时间去处理更重要的任务，而不太重要的任务，则可以先让出一部分资源。线程的优先级一般分为以下三种：

- MIN_PRIORITY  最低优先级
- MAX_PRIORITY  最高优先级
- NOM_PRIORITY  常规优先级

```java
public static void main(String[] args) {
    Thread t = new Thread(() -> {
        System.out.println("线程开始运行！");
    });
    t.start();
    t.setPriority(Thread.MIN_PRIORITY);  //通过使用setPriority方法来设定优先级
}
```

优先级越高的线程，获得CPU资源的概率会越大，并不是说一定优先级越高的线程越先执行！





#### 线程的礼让和加入

:star: 使用`yiled`让位操作，将当前`Cpu`资源让位给同等优先级的线程，并不是直接暂停当前让位的线程，而是尽可能的让其他线程拿到资源的时间比让位的进程多(我是这样理解的)

:one: 让位之后执行`t2`执行一段时间之后在回到`t1`，然后继续满足条件继续让位，在让位之后，尽可能多的在执行线程2的内容

```java
public static void main(String[] args) {
    Thread t1 = new Thread(() -> {
        System.out.println("线程1开始运行！");
        for (int i = 0; i < 50; i++) {
            if(i % 5 == 0) {
                System.out.println("让位！");
                Thread.yield();
            }
            System.out.println("1打印："+i);
        }
        System.out.println("线程1结束！");
    });
    Thread t2 = new Thread(() -> {
        System.out.println("线程2开始运行！");
        for (int i = 0; i < 50; i++) {
            System.out.println("2打印："+i);
        }
    });
    t1.start();
    t2.start();
}
```

:star: 当一个进程需要等待另外一个进程执行完毕后在操作可以使用`join`，也就是将被等待进程加入到等待进程，线程的加入只是等待另一个线程的完成，并不是将另一个线程和当前线程合并！

```java
public static void main(String[] args) {
    Thread t1 = new Thread(() -> {
        System.out.println("线程1开始运行！");
        for (int i = 0; i < 50; i++) {
            System.out.println("1打印："+i);
        }
        System.out.println("线程1结束！");
    });
    Thread t2 = new Thread(() -> {
        System.out.println("线程2开始运行！");
        for (int i = 0; i < 50; i++) {
            System.out.println("2打印："+i);
            if(i == 10){
                try {
                    System.out.println("线程1加入到此线程！");
                    t1.join();    //在i==10时，让线程1加入，先完成线程1的内容，在继续当前内容
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    });
    t1.start();
    t2.start();
}
```



#### 线程锁和线程同步

:star: :question: 如果让两个线程，都访问一个变量，并且线程内部都是将这个变量执行`10000`次，会发生什么操作？

![image-20221004203914215](https://image.itbaima.cn/markdown/2022/10/04/ZvI8neF3tdGJwS4.png)

线程之间的共享变量（比如之前悬念中的value变量）存储在主内存（main memory）中，每个线程都有一个私有的工作内存（本地内存），工作内存中存储了该线程以读/写共享变量的副本。它类似于我们在`计算机组成原理`中学习的多核心处理器高速缓存机制：

![image-20221004204209038](https://image.itbaima.cn/markdown/2022/10/04/SKlbIZyvxMnauLJ.png)

高速缓存通过保存内存中数据的副本来提供更加快速的数据访问，但是如果多个处理器的运算任务都涉及同一块内存区域，就可能导致各自的高速缓存数据不一致，在写回主内存时就会发生冲突，这就是引入高速缓存引发的新问题，称之为：缓存一致性。

实际上，Java的内存模型也是这样类似设计的，当我们同时去操作一个共享变量时，如果仅仅是读取还好，但是如果同时写入内容，就会出现问题！好比说一个银行，如果我和我的朋友同时在银行取我账户里面的钱，难道取1000还可能吐2000出来吗？我们需要一种更加安全的机制来维持秩序，保证数据的安全性！

:one: 会发现按照道理来说应该是20000，但是有些时候可能输出不到20000，当然有些情况如果电脑情况比较好，可能`t1`执行完成，`t2`才执行，可能就会出现20000

这是因为每个线程在运行的时候会得到一个工作内存，而变量`i`是存储在主内存中的，它们在对`i`进行操作的时候，就会复制一个i到线程里面去执行线程里面的`i`，执行完毕才会将线程里面的`i`的值返回给主内存中的`i`，**这就会出现异步**问题

```java
    private  static  int i =0;
    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(() -> {
            for (int j = 0; j < 10000; j++) {
                i++;
            }
        });
        Thread t2 = new Thread(() -> {
            for (int j = 0; j < 10000; j++) {
                i++;
            }
        });
        t1.start();
        t2.start();
        Thread.sleep(2000); //暂停两秒，让线程执行
        System.out.println(i); //输出i
    }
```

 :star: 解决办法是，在某个进程使用主内存变量或者全局变量的时候，就给这个变量加个锁，同一时间只能由一个线程访问和操作

:one: 同步代码块`synchronized`,这下无论你如何执行都会是2000了,因为在同步代码块执行过程中，拿到了我们传入对象或类的锁（传入的如果是对象，就是对象锁，不同的对象代表不同的对象锁，如果是类，就是类锁，类锁只有一个，实际上类锁也是对象锁(入`Main.class`)，是Class类实例，但是Class类实例同样的类无论怎么获取都是同一个），但是注意两个线程必须使用同一把锁！

当一个线程进入到同步代码块时，会获取到当前的锁，而这时如果其他使用同样的锁的同步代码块也想执行内容，就必须等待当前同步代码块的内容执行完毕，在执行完毕后会自动释放这把锁，而其他的线程才能拿到这把锁并开始执行同步代码块里面的内容（实际上`synchronized`是一种悲观锁，随时都认为有其他线程在对数据进行修改;

:star: 必须要是同一把锁才可以

```java
    private  static  int i =0;
    public static void main(String[] args) throws InterruptedException {
        Object o = new Object(); //创建一个对象使用它的锁
        Thread t1 = new Thread(() -> {
            for (int j = 0; j < 10000; j++) {
                synchronized (o) { //使用synchronized创建同步代码块
                    i++;
                }
            }
        });
        Thread t2 = new Thread(() -> {
            for (int j = 0; j < 10000; j++) {
                synchronized (o) {
                    i++;
                }
            }
        });
        t1.start();
        t2.start();
        Thread.sleep(2000); //暂停两秒，让线程执行
        System.out.println(i); //输出i
    }
```

:two: 有个更加直观的列子

```java
    public static void main(String[] args) throws InterruptedException {
        Object o = new Object(); //创建一个对象使用它的锁
        Thread t1 = new Thread(() -> {
            System.out.println("线程1开始执行");
            synchronized (o) { //使用synchronized创建同步代码块
                for (int j = 0; j < 5; j++) {
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        throw new RuntimeException(e);
                    }
                    System.out.println("线程1运行中...");
                }
                System.out.println("线程1结束");
            }
        });
        Thread t2 = new Thread(() -> {
            System.out.println("线程2开始执行");
            synchronized (o) { //使用synchronized创建同步代码块
                for (int j = 0; j < 5; j++) {
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        throw new RuntimeException(e);
                    }
                    System.out.println("线程2运行中...");
                }
                System.out.println("线程2结束");
            }
        });
        t1.start();
        t2.start();
    }

//输出:
线程1开始执行
线程2开始执行
线程1运行中... 
线程1运行中...
线程1运行中...
线程1运行中...
线程1运行中...
线程1结束
线程2运行中...//线程二等待线程一
线程2运行中...
线程2运行中...
线程2运行中...
线程2运行中...
线程2结束
```

:three:如果不是同一把锁

```java
 public static void main(String[] args) throws InterruptedException {
        Object o = new Object(); //创建一个对象使用它的锁
        Thread t1 = new Thread(() -> {
            System.out.println("线程1开始执行");
            synchronized (o) { //使用synchronized创建同步代码块
                for (int j = 0; j < 5; j++) {
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        throw new RuntimeException(e);
                    }
                    System.out.println("线程1运行中...");
                }
                System.out.println("线程1结束");
            }
        });
        Thread t2 = new Thread(() -> {
            System.out.println("线程2开始执行");
            synchronized (new Object()) { //不是同一把锁
                for (int j = 0; j < 5; j++) {
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        throw new RuntimeException(e);
                    }
                    System.out.println("线程2运行中...");
                }
                System.out.println("线程2结束");
            }
        });
        t1.start();
        t2.start();
    }
```

:star: `synchronized`还可以锁方法，这个方法只能拿有一个线程调用

```java
 private static int value = 0;

    private static synchronized void add(){ //直接使用的方法锁，静态方法
        value++;
    }

    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 10000; i++) add();
            System.out.println("线程1完成");
        });
        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 10000; i++) add();
            System.out.println("线程2完成");
        });
        Thread t3 = new Thread(() -> {
            for (int i = 0; i < 10000; i++)
                synchronized(Main.class){ //因为这里是静态方法所以说是当前这个类的锁
                    value++;
                }
            System.out.println("线程3完成");
        });
        t1.start();
        t2.start();
        t3.start();
        Thread.sleep(1000);  //主线程停止1秒，保证两个线程执行完成
        System.out.println(value);
    }
```

:one: 如果不是静态方法就需要创建对象，使用对象锁

```java
    private static int value = 0;

    private synchronized void add(){ //直接使用的方法锁，成员方法
        value++;
    }

    public static void main(String[] args) throws InterruptedException {
        Main main = new Main();
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 10000; i++) main.add();
            System.out.println("线程1完成");
        });
        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 10000; i++) main.add();
            System.out.println("线程2完成");
        });
        Thread t3 = new Thread(() -> {
            for (int i = 0; i < 10000; i++)
                synchronized(main){ //这个是成员方法使用对象锁
                    value++;
                }
            System.out.println("线程3完成");
        });
        t1.start();
        t2.start();
        t3.start();
        Thread.sleep(1000);  //主线程停止1秒，保证两个线程执行完成
        System.out.println(value);
    }
```



#### 死锁

:star:其实死锁的概念在`操作系统`中也有提及，它是指两个线程相互持有对方需要的锁，但是又迟迟不释放，导致程序卡住：

![image-20221004205058223](https://image.itbaima.cn/markdown/2022/10/04/Ja6TPO23wCI8pvn.png)

:one: 程序A那拿到了锁A，程序B拿到了锁B，程序A执行一段时间后，有段代码需要拿到锁B才可以继续执行，而程序B也是如此，需要拿到锁A才可以继续运行，它们两互相僵持不下，本来可以退一步海阔天空，但是它们一直僵持

程序演示

```java
public static void main(String[] args) throws InterruptedException {
    Object o1 = new Object(); //锁1
    Object o2 = new Object(); //锁2
    Thread t1 = new Thread(() -> {
        synchronized (o1){ //先拿到锁一
            try {
                Thread.sleep(1000);
                synchronized (o2){ //需要锁二才可以执行，而锁2需要等待t2运行完成才可以释放
                    System.out.println("线程1");
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    });
    Thread t2 = new Thread(() -> {
        synchronized (o2){ //先拿到锁2
            try {
                Thread.sleep(1000);
                synchronized (o1){ //需要锁1才可以继续执行，而锁1需要等待t1运行完成才可以释放
                    System.out.println("线程2");
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    });
    t1.start();
    t2.start();
}
```

:star: 通过`jstack`来检查java程序中是否有死锁，`jps`会显示`java`进程的进程号

:one: 先运行上面的程序使用`jps`查看进程号

```java
31936 
21444 Main
26812 Launcher
9692 Jps
```

:two: 使用`jstack`查看

```java
# jstack 21444

2024-04-25 21:06:36
Full thread dump Java HotSpot(TM) 64-Bit Server VM (25.131-b11 mixed mode):

"DestroyJavaVM" #22 prio=5 os_prio=0 tid=0x0000000003003800 nid=0x704c waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"Thread-1" #21 prio=5 os_prio=0 tid=0x0000000021312000 nid=0x5ff8 waiting for monitor entry [0x0000000021b8f000]
   java.lang.Thread.State: BLOCKED (on object monitor)
        at Main.lambda$main$1(Main.java:24)
        - waiting to lock <0x000000076e3a1f78> (a java.lang.Object)
        - locked <0x000000076e3a1f88> (a java.lang.Object)
        at Main$$Lambda$2/1324119927.run(Unknown Source)
        at java.lang.Thread.run(Thread.java:748)

"Thread-0" #20 prio=5 os_prio=0 tid=0x000000002130e800 nid=0x6110 waiting for monitor entry [0x0000000021a8f000]
   java.lang.Thread.State: BLOCKED (on object monitor)
        at Main.lambda$main$0(Main.java:12)
        - waiting to lock <0x000000076e3a1f88> (a java.lang.Object)
        - locked <0x000000076e3a1f78> (a java.lang.Object)
        at Main$$Lambda$1/295530567.run(Unknown Source)
        at java.lang.Thread.run(Thread.java:748)

"Service Thread" #19 daemon prio=9 os_prio=0 tid=0x000000001e785000 nid=0x7fa0 runnable [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"C1 CompilerThread11" #18 daemon prio=9 os_prio=2 tid=0x000000001e6b6000 nid=0x6818 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"C1 CompilerThread10" #17 daemon prio=9 os_prio=2 tid=0x000000001e6b7800 nid=0x5c08 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"C1 CompilerThread9" #16 daemon prio=9 os_prio=2 tid=0x000000001e6b5800 nid=0x71d8 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"C1 CompilerThread8" #15 daemon prio=9 os_prio=2 tid=0x000000001e6b4000 nid=0x179c waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"C2 CompilerThread7" #14 daemon prio=9 os_prio=2 tid=0x000000001e6b4800 nid=0x4070 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"C2 CompilerThread6" #13 daemon prio=9 os_prio=2 tid=0x000000001e6a8800 nid=0x7804 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"C2 CompilerThread5" #12 daemon prio=9 os_prio=2 tid=0x000000001e69e000 nid=0x77d8 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"C2 CompilerThread4" #11 daemon prio=9 os_prio=2 tid=0x000000001e696000 nid=0x7d48 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"C2 CompilerThread3" #10 daemon prio=9 os_prio=2 tid=0x000000001e695800 nid=0x39b8 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"C2 CompilerThread2" #9 daemon prio=9 os_prio=2 tid=0x000000001e694800 nid=0x6a00 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"C2 CompilerThread1" #8 daemon prio=9 os_prio=2 tid=0x000000001e694000 nid=0x5378 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"C2 CompilerThread0" #7 daemon prio=9 os_prio=2 tid=0x000000001e682800 nid=0x50d4 waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"Monitor Ctrl-Break" #6 daemon prio=5 os_prio=0 tid=0x000000001e67f800 nid=0x5f8c runnable [0x000000002028e000]
   java.lang.Thread.State: RUNNABLE
        at java.net.SocketInputStream.socketRead0(Native Method)
        at java.net.SocketInputStream.socketRead(SocketInputStream.java:116)
        at java.net.SocketInputStream.read(SocketInputStream.java:171)
        at java.net.SocketInputStream.read(SocketInputStream.java:141)
        at sun.nio.cs.StreamDecoder.readBytes(StreamDecoder.java:284)
        at sun.nio.cs.StreamDecoder.implRead(StreamDecoder.java:326)
        at sun.nio.cs.StreamDecoder.read(StreamDecoder.java:178)
        - locked <0x000000076e27d6d0> (a java.io.InputStreamReader)
        at java.io.InputStreamReader.read(InputStreamReader.java:184)
        at java.io.BufferedReader.fill(BufferedReader.java:161)
        at java.io.BufferedReader.readLine(BufferedReader.java:324)
        - locked <0x000000076e27d6d0> (a java.io.InputStreamReader)
        at java.io.BufferedReader.readLine(BufferedReader.java:389)
        at com.intellij.rt.execution.application.AppMainV2$1.run(AppMainV2.java:53)

"Attach Listener" #5 daemon prio=5 os_prio=2 tid=0x000000001e5eb000 nid=0x7c6c waiting on condition [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"Signal Dispatcher" #4 daemon prio=9 os_prio=2 tid=0x000000001e640800 nid=0x49ac runnable [0x0000000000000000]
   java.lang.Thread.State: RUNNABLE

"Finalizer" #3 daemon prio=8 os_prio=1 tid=0x000000001e5c6800 nid=0x7c28 in Object.wait() [0x000000001ff2e000]
   java.lang.Thread.State: WAITING (on object monitor)
        at java.lang.Object.wait(Native Method)
        - waiting on <0x000000076e108ec8> (a java.lang.ref.ReferenceQueue$Lock)
        at java.lang.ref.ReferenceQueue.remove(ReferenceQueue.java:143)
        - locked <0x000000076e108ec8> (a java.lang.ref.ReferenceQueue$Lock)
        at java.lang.ref.ReferenceQueue.remove(ReferenceQueue.java:164)
        at java.lang.ref.Finalizer$FinalizerThread.run(Finalizer.java:209)

"Reference Handler" #2 daemon prio=10 os_prio=2 tid=0x000000001cb6f800 nid=0xc10 in Object.wait() [0x000000001fe2f000]
   java.lang.Thread.State: WAITING (on object monitor)
        at java.lang.Object.wait(Native Method)
        - waiting on <0x000000076e106b68> (a java.lang.ref.Reference$Lock)
        at java.lang.Object.wait(Object.java:502)
        at java.lang.ref.Reference.tryHandlePending(Reference.java:191)
        - locked <0x000000076e106b68> (a java.lang.ref.Reference$Lock)
        at java.lang.ref.Reference$ReferenceHandler.run(Reference.java:153)

"VM Thread" os_prio=2 tid=0x000000001e5a4800 nid=0x7130 runnable

"GC task thread#0 (ParallelGC)" os_prio=0 tid=0x0000000003018000 nid=0x6ba0 runnable

"GC task thread#1 (ParallelGC)" os_prio=0 tid=0x000000000301a000 nid=0x1f28 runnable

"GC task thread#2 (ParallelGC)" os_prio=0 tid=0x000000000301b800 nid=0x7738 runnable

"GC task thread#3 (ParallelGC)" os_prio=0 tid=0x000000000301e000 nid=0x7184 runnable

"GC task thread#4 (ParallelGC)" os_prio=0 tid=0x0000000003020000 nid=0x686c runnable

"GC task thread#5 (ParallelGC)" os_prio=0 tid=0x0000000003021000 nid=0x6d1c runnable

"GC task thread#6 (ParallelGC)" os_prio=0 tid=0x0000000003024800 nid=0x6880 runnable

"GC task thread#7 (ParallelGC)" os_prio=0 tid=0x0000000003025800 nid=0x640 runnable

"GC task thread#8 (ParallelGC)" os_prio=0 tid=0x0000000003026800 nid=0x7808 runnable

"GC task thread#9 (ParallelGC)" os_prio=0 tid=0x0000000003028000 nid=0x615c runnable

"GC task thread#10 (ParallelGC)" os_prio=0 tid=0x0000000003029000 nid=0x70bc runnable 

"GC task thread#11 (ParallelGC)" os_prio=0 tid=0x000000000302c000 nid=0x3178 runnable

"GC task thread#12 (ParallelGC)" os_prio=0 tid=0x000000000302d800 nid=0x7e98 runnable

"VM Periodic Task Thread" os_prio=2 tid=0x000000001e7ab800 nid=0x7e9c waiting on condition

JNI global references: 318

//发现了一个死锁
Found one Java-level deadlock:
=============================
"Thread-1":
  waiting to lock monitor 0x000000001cb761a8 (object 0x000000076e3a1f78, a java.lang.Object),
  which is held by "Thread-0"
"Thread-0":
  waiting to lock monitor 0x000000001cb73918 (object 0x000000076e3a1f88, a java.lang.Object),
  which is held by "Thread-1"

//死锁发生位置
Java stack information for the threads listed above:
===================================================
"Thread-1":
        at Main.lambda$main$1(Main.java:24)
        - waiting to lock <0x000000076e3a1f78> (a java.lang.Object)
        - locked <0x000000076e3a1f88> (a java.lang.Object)
        at Main$$Lambda$2/1324119927.run(Unknown Source)
        at java.lang.Thread.run(Thread.java:748)
"Thread-0":
        at Main.lambda$main$0(Main.java:12)
        - waiting to lock <0x000000076e3a1f88> (a java.lang.Object)
        - locked <0x000000076e3a1f78> (a java.lang.Object)
        at Main$$Lambda$1/295530567.run(Unknown Source)
        at java.lang.Thread.run(Thread.java:748)

Found 1 deadlock.


```

:three: 还可以使用`jconsole`来查看，它是一个图形化界面

![image-20240425210909340](https://image-1305907375.cos.ap-chengdu.myqcloud.com/Flower_bound_manage_boundimage-20240425210909340.png)

![image-20240425210944437](https://image-1305907375.cos.ap-chengdu.myqcloud.com/Flower_bound_manage_boundimage-20240425210944437.png)

![image-20240425211031646](https://image-1305907375.cos.ap-chengdu.myqcloud.com/Flower_bound_manage_boundimage-20240425211031646.png)

> 因此，前面说不推荐使用 `suspend()`去挂起线程的原因，是因为`suspend()`在使线程暂停的同时，并不会去释放任何锁资源。其他线程都无法访问被它占用的锁。直到对应的线程执行`resume()`方法后，被挂起的线程才能继续，从而其它被阻塞在这个锁的线程才可以继续执行。但是，如果`resume()`操作出现在`suspend()`之前执行，那么线程将一直处于挂起状态，同时一直占用锁，这就产生了死锁。



#### wait和notify方法

> 操作系统中的PV操作

:star: Object类还有三个方法我们从来没有使用过，分别是`wait()`、`notify()`以及`notifyAll()`，他们其实是需要配合`synchronized`来使用的（实际上锁就是依附于对象存在的，每个对象都应该有针对于锁的一些操作，所以说就这样设计了）当然，只有在同步代码块中才能使用这些方法，正常情况下会报错，我们来看看他们的作用是什么

:star: `wait`相当于暂停这个线程，而且释放了它持有的锁，使得其他线程可以获取到它持有的锁，当其他线程调用`notify`方法后，就会唤醒`wait`的线程(但是不会立即释放锁)，但是必须要等到线程程执行结束才释放

```java
import java.util.TreeMap;

public class Main {
    public static void main(String[] args) throws InterruptedException {
        Object o1 = new Object(); //锁1
        Object o2 = new Object(); //锁2
        Thread t1 = new Thread(() -> {
            synchronized (o1){ //先拿到锁一
                try {
                    Thread.sleep(1000);
                    System.out.println("线程一开始拿到o1锁");
                    System.out.println("开始等待");
                    o1.wait(); //暂停进程，并释放锁
                    synchronized (o2){ //需要锁二才可以执行，而锁2需要等待t2运行完成才可以释放
                        System.out.println("线程1拿到o2锁");
                    }
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            System.out.println("线程一结束");
        });
        Thread t2 = new Thread(() -> {
            synchronized (o2){ //先拿到锁2
                try {
                    Thread.sleep(1000);
                    System.out.println("线程二开始拿到o2锁");
                    synchronized (o1){ //需要锁1才可以继续执行，而锁1需要等待t1运行完成才可以释放
                        System.out.println("线程2拿到o1锁");
                        o1.notify();
                    }
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            System.out.println("线程二结束");
        });
        t1.start();
        t2.start();
    }
}


//执行:
线程一开始拿到o1锁
开始等待
线程二开始拿到o2锁
线程2拿到o1锁
线程二结束
线程1拿到o2锁
线程一结束
```

:star: 必须是在持有锁的情况下使用(同步代码块内)，否则会抛出异常，这个也很好理解，就是你都没拿到这个锁，你肯定就不能执行啊

:star: 当很多线程都是通过`wait`方法暂停就可以直接使用`notifyAll`唤醒全部的，而`notify`是随机唤醒一个

:star: 多种`wait`方法

| 方法                            | 描述                                                         | 参数                                             | 示例                      |
| ------------------------------- | ------------------------------------------------------------ | ------------------------------------------------ | ------------------------- |
| `wait()`                        | 导致当前线程等待，直到另一个线程调用此对象的 `notify()` 方法或 `notifyAll()` 方法，或者被其他线程中断。 | 无                                               | `obj.wait();`             |
| `wait(long timeout)`            | 导致当前线程等待，直到另一个线程调用此对象的 `notify()` 方法或 `notifyAll()` 方法，或者经过了指定的毫秒数。 | `timeout` - 毫秒数，等待的最长时间               | `obj.wait(1000);`         |
| `wait(long timeout, int nanos)` | 导致当前线程等待，直到另一个线程调用此对象的 `notify()` 方法或 `notifyAll()` 方法，或者经过了指定的毫秒数加上纳秒数后被中断。 | `timeout` - 毫秒数<br>`nanos` - 纳秒数，0-999999 | `obj.wait(1000, 500000);` |

:one: 使用实例:

以下是一个简单的示例，展示了如何在`Object`类的方法中使用`wait()`和`notifyAll()`方法来实现线程间的同步。

```java
public class WaitNotifyExample {
    public static void main(String[] args) {
        final Object monitor = new Object();

        Thread producer = new Thread(new Runnable() {
            @Override
            public void run() {
                synchronized (monitor) {
                    System.out.println("Producer is waiting to produce.");
                    try {
                        // 等待，直到消费者消费完毕并通知
                        monitor.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println("Producing...");
                }
            }
        });

        Thread consumer = new Thread(new Runnable() {
            @Override
            public void run() {
                synchronized (monitor) {
                    System.out.println("Consuming...");
                    // 消费完成，通知生产者可以继续生产
                    monitor.notifyAll();
                    System.out.println("Notified producer.");
                }
            }
        });

        producer.start();
        // 确保生产者线程先开始等待
        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        consumer.start();
    }
}
```

**注意**：在实际应用中，直接使用`Object`的等待/通知机制需要非常小心，因为不当的使用很容易导致死锁或不响应。通常推荐使用`java.util.concurrent`包下的高级并发工具类，如`Semaphore`, `CountDownLatch`, `CyclicBarrier`等，它们提供了更强大且易于控制的同步机制。

:star: `wait`也是可以响应异常操作的

```Java
    public static void main(String[] args) throws InterruptedException {
        Object o1 = new Object(); //锁1
        Thread t1 = new Thread(() -> {
            synchronized (o1){ //先拿到锁一
                try {
                    System.out.println("线程一开始拿到o1锁");
                    System.out.println("开始等待");
                    o1.wait(); //暂停进程，并释放锁
                } catch (InterruptedException e) {
                    System.out.println("等待的过程中被中断了");
                    e.printStackTrace();
                }
            }
            System.out.println("线程一结束");
        });
        t1.start();
        t1.interrupt();
    }
```



🤣下面这个代码我刚刚开始写的时候看他输出笑了一下

从您提供的执行输出顺序来看，线程3的执行情况并未完整展示，但我们可以基于现有信息推测可能的原因：

1. **线程调度**: 线程的执行顺序受到操作系统线程调度策略的影响，可能导致线程3在等待执行机会时，其他线程（特别是线程1和线程2）已经完成了它们的任务并退出。如果在您的观察时段内，线程调度器没有给线程3足够的CPU时间片，它可能还没来得及完成其执行流程。

2. **锁的获取**: 根据输出，线程3在尝试获取`o1`锁之后进入了等待状态。如果在线程1和线程2执行完毕后，没有其他线程持有或再次请求了`o1`锁，理论上线程3应该能够被唤醒并继续执行，获取`o2`锁，然后结束。如果输出没有显示线程3的后续步骤，可能是因为在您观测时点，它还在等待被唤醒，或者唤醒后因某种原因（如再次尝试获取锁失败、线程被中断等）未能顺利执行至结束。

3. **潜在的死锁或竞态条件**: 虽然根据您给出的输出，这次执行似乎避免了预期的死锁，但代码的设计中仍存在死锁的风险，尤其是当线程调度的时序稍有不同，可能导致线程3在等待`o1`或`o2`锁时陷入死锁状态。如果线程2和线程1的执行顺序恰好导致了`o1`和`o2`锁的永久锁定，那么线程3将无法继续执行。

4. **输出同步问题**: 如果输出语句没有使用同步机制（如`synchronized`块），在多线程环境下可能存在输出混乱，导致某些线程的输出没有完整打印出来。

为确保线程3能够执行完毕并打印结束信息，建议检查和调整代码中的同步逻辑，以及考虑使用更高级的并发工具来管理线程间的协调，同时确保输出逻辑的同步以准确反映线程执行情况。

```java
import java.util.TreeMap;

public class Main {
    public static void main(String[] args) throws InterruptedException {
        Object o1 = new Object(); //锁1
        Object o2 = new Object(); //锁2
        Thread t1 = new Thread(() -> {
            synchronized (o1){ //先拿到锁一
                try {
                    Thread.sleep(1000);
                    System.out.println("线程一开始拿到o1锁");
                    System.out.println("开始等待");
                    o1.wait(); //暂停进程，并释放锁
                    synchronized (o2){ //需要锁二才可以执行，而锁2需要等待t2运行完成才可以释放
                        System.out.println("线程1拿到o2锁");
                    }
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            System.out.println("线程一结束");
        });
        Thread t2 = new Thread(() -> {
            synchronized (o2){ //先拿到锁2
                try {
                    Thread.sleep(1000);
                    System.out.println("线程二开始拿到o2锁");
                    synchronized (o1){ //需要锁1才可以继续执行，而锁1需要等待t1运行完成才可以释放
                        System.out.println("线程2拿到o1锁");
                        o1.notify();
                    }
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            System.out.println("线程二结束");
        });

        Thread t3 = new Thread(() -> {
            synchronized (o1){ //等待进程拿到锁一
                try {
                    Thread.sleep(1000);
                    System.out.println("线程3开始拿到o1锁");
                    System.out.println("开始等待");
                    o1.wait(); //暂停进程，并释放锁
                    synchronized (o2){
                        System.out.println("线程3拿到o2锁");
                    }
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            System.out.println("线程3结束");
        });
        t1.start();
        t2.start();
        t3.start();
    }
}


//执行
线程一开始拿到o1锁
开始等待
线程二开始拿到o2锁
线程3开始拿到o1锁
开始等待
线程2拿到o1锁
线程二结束
线程1拿到o2锁 
线程一结束
```



#### ThreadLocal的使用

:star: 每个线程都有工作内存，使用`TreadLocal`类来在工作内存中在创建线程供呈现出自己使用

```java
    public static void main(String[] args) throws InterruptedException {
        ThreadLocal<String> local = new ThreadLocal<>();
        local.set("Hello World");
        System.out.println(local.get()); //在当前线程是可以取到数据

        new  Thread(() -> System.out.println(local.get())).start(); //在其他线程取不到
    }

//输出
Hello World
null
```

```java
public static void main(String[] args) throws InterruptedException {
    ThreadLocal<String> local = new ThreadLocal<>();  //注意这是一个泛型类，存储类型为我们要存放的变量类型
    Thread t1 = new Thread(() -> {
        local.set("lbwnb");   //将变量的值给予ThreadLocal
        System.out.println("线程1变量值已设定！");
        try {
            Thread.sleep(2000);    //间隔2秒
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("线程1读取变量值：");
        System.out.println(local.get());   //尝试获取ThreadLocal中存放的变量
    });
    Thread t2 = new Thread(() -> {
        local.set("yyds");   //将变量的值给予ThreadLocal
        System.out.println("线程2变量值已设定！");
    });
    t1.start();
    Thread.sleep(1000);    //间隔1秒
    t2.start();
}
```

:star: 希望父线程的`ThreadLocal`设置的变量可以给子线程使用我们可以使用`InheritableThreadLocal`来解决：

```java
public static void main(String[] args) {
    ThreadLocal<String> local = new InheritableThreadLocal<>();
    Thread t = new Thread(() -> {
       local.set("lbwnb");
        new Thread(() -> {
            System.out.println(local.get());
        }).start();
    });
    t.start();
}
```



#### 定时器

:star: 我们有时候会有这样的需求，我希望定时执行任务，比如3秒后执行，其实我们可以通过使用`Thread.sleep()`来实现：

:two: 我们通过自行封装一个`TimerTask`类，并在启动时，先休眠3秒钟，再执行我们传入的内容。那么现在我们希望，能否循环执行一个任务呢？比如我希望每隔1秒钟执行一次代码，这样该怎么做呢？

```java
public static void main(String[] args) {
    new TimerTask(() -> System.out.println("我是定时任务！"), 3000).start();   //创建并启动此定时任务
}

//自定义了类，在类中执行star方法执行休眠
static class TimerTask{
    Runnable task;
    long time;

    public TimerTask(Runnable runnable, long time){
        this.task = runnable;
        this.time = time;
    }

    public void start(){
        new Thread(() -> {
            try {
                Thread.sleep(time);
                task.run();   //休眠后再运行
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();
    }
}
```

```java
public static void main(String[] args) {
    new TimerLoopTask(() -> System.out.println("我是定时任务！"), 3000).start();   //创建并启动此定时任务
}

static class TimerLoopTask{
    Runnable task;
    long loopTime;

    public TimerLoopTask(Runnable runnable, long loopTime){
        this.task = runnable;
        this.loopTime = loopTime;
    }

    public void start(){
        new Thread(() -> {
            try {
                while (true){   //无限循环执行
                    Thread.sleep(loopTime);
                    task.run();   //休眠后再运行
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();
    }
}
```

:star: 但是上面两种方法终究都是自己定义了，很多方面可能没有考虑全面，`Java`为我们自己提供了一个框架，用于处理定时任务

:one: 使用`Timer`创建一个定时对象,使用`schedule`来调度一个任务，需要传入一个`TimerTask`类，我们可以通过此对象来创建任意类型的定时任务，包延时任务、循环定时任务等

```java
public static void main(String[] args) {
    Timer timer = new Timer();    //创建定时器对象
    timer.schedule(new TimerTask() {   //注意这个是一个抽象类，不是接口，无法使用lambda表达式简化，只能使用匿名内部类
        @Override
        public void run() {
            System.out.println(Thread.currentThread().getName());    //打印当前线程名称
        }
    }, 1000);    //执行一个延时任务，设定延迟1s
}
```

:two: 会发现线程并没有终止，在`Java`中需要非守护线程全部结束，运行才会结束，并不是主线程结束了才结束，因为Timer内存维护了一个任务队列和一个工作线程，

```java
public class Timer {
    /**
     * The timer task queue.  This data structure is shared with the timer
     * thread.  The timer produces tasks, via its various schedule calls,
     * and the timer thread consumes, executing timer tasks as appropriate,
     * and removing them from the queue when they're obsolete.
     */
    private final TaskQueue queue = new TaskQueue(); //任务队列

    /**
     * The timer thread.
     */
    private final TimerThread thread = new TimerThread(queue); //任务线程
  
		...
}
```

:three: 如果我想要这个程序没隔一段时间执行一次，可以实现下面这个构建方法，在传入一个每隔一段时间执行一次

```java
//源代码   
	public void schedule(TimerTask task, long delay, long period) {
        if (delay < 0)
            throw new IllegalArgumentException("Negative delay.");
        if (period <= 0)
            throw new IllegalArgumentException("Non-positive period.");
        sched(task, System.currentTimeMillis()+delay, -period);
    }
```

```java
    public static void main(String[] args) {
        Timer timer = new Timer();    //创建定时器对象
        timer.schedule(new TimerTask() {
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName());    //打印当前线程名称
            }
        }, 1000,1000);    //执行一个延时任务,延迟1s执行，且每1s执行一次
    }
```

:four: 使用`cancel`方法终止`timer`

```java
    public static void main(String[] args) throws InterruptedException {
        Timer timer = new Timer();    //创建定时器对象
        timer.schedule(new TimerTask() {   //注意这个是一个抽象类，不是接口，无法使用lambda表达式简化，只能使用匿名内部类
            @Override
            public void run() {
                System.out.println(Thread.currentThread().getName());    //打印当前线程名称
            }
        }, 1000,1000);    //执行一个延时任务,延迟1s执行，且每1s执行一次
        Thread.sleep(5000); //五秒后终止
        timer.cancel();
    }
```



:star: 在什么的`Timer`源码中，会有个`TimerThread`，它继承自`Thread`是新创建的线程，在构造时候自动启动

```java
    public Timer(String name) {
        thread.setName(name);
        thread.start(); //启动线程
    } 
```

:one: 而它的run方法会循环地读取队列中是否还有任务，如果有任务依次执行，没有的话就暂时处于休眠状态：

```java
public void run() {
    try {
        mainLoop();
    } finally {
        // Someone killed this Thread, behave as if Timer cancelled
        synchronized(queue) {
            newTasksMayBeScheduled = false;
            queue.clear();  // Eliminate obsolete references
        }
    }
}

/**
 * The main timer loop.  (See class comment.)
 */
private void mainLoop() {
  try {
       TimerTask task;
       boolean taskFired;
       synchronized(queue) {
         	// Wait for queue to become non-empty
          while (queue.isEmpty() && newTasksMayBeScheduled)   //当队列为空同时没有被关闭时，会调用wait()方法暂时处于等待状态，当有新的任务时，会被唤醒。
                queue.wait();
          if (queue.isEmpty())
             break;    //当被唤醒后都没有任务时，就会结束循环，也就是结束工作线程
                      ...
}
```

:two: `newTasksMayBeScheduled`实际上就是标记当前定时器是否关闭，当它为false时，表示已经不会再有新的任务到来，也就是关闭，我们可以通过调用`cancel()`方法来关闭它的工作线程：

```java
public void cancel() {
    synchronized(queue) {
        thread.newTasksMayBeScheduled = false;
        queue.clear();
        queue.notify();  //唤醒wait使得工作线程结束
    }
}
```

4

#### 守护线程

:warning: 不要把操作系统重的守护进程和守护线程相提并论！

:star: 守护进程在后台运行运行，不需要和用户交互，本质和普通进程类似。而守护线程就不一样了，当其他所有的非守护线程结束之后，守护线程自动结束，也就是说，Java中所有的线程都执行完毕后，守护线程自动结束，因此守护线程不适合进行IO操作，只适合打打杂，因为分配给守护进程的资源一般都比较小；

:one: 使用`setDaemon`设置守护线程，设置了守护线程后，`main`线程（也就是主线程），主线程结束后，守护线程就会自动结束，即便他还在继续运行，如果不设置为守护线程，那么主线程结束后，子线程还会继续运行

```java
public static void main(String[] args) throws InterruptedException{
    Thread t = new Thread(() -> {
        while (true){
            try {
                System.out.println("程序正常运行中...");
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    });
    t.setDaemon(true);   //设置为守护线程（必须在开始之前，中途是不允许转换的）
    t.start();
    for (int i = 0; i < 5; i++) {
        Thread.sleep(1000);
    }
}
```

:star: 在守护线程中产生的新线程也是守护的：

```java
public static void main(String[] args) throws InterruptedException{
    Thread t = new Thread(() -> {
        Thread it = new Thread(() -> {
            while (true){
                try {
                    System.out.println("程序正常运行中...");
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        it.start();
    });
    t.setDaemon(true);   //设置为守护线程（必须在开始之前，中途是不允许转换的）
    t.start();
    for (int i = 0; i < 5; i++) {
        Thread.sleep(1000);
    }
}
```



#### 集合的多线程

:star: 之前在学习集合的时候，提到过有个`spliterator`,和`parallelStream`，当时只是提了一下

可拆分迭代器（`Splitable` `Iterator`）和`Iterator`一样，`Spliterator`也用于遍历数据源中的元素，但它是为了并行执行而设计的。Java 8已经为集合框架中包含的所有数据结构提供了一个默认的`Spliterator`实现。在集合跟接口Collection中提供了一个`spliterator()`方法用于获取可拆分迭代器

```java
    //与迭代器作用相同，但是是并行执行的，我们会在下一章多线程部分中进行介绍
    @Override
    default Spliterator<E> spliterator() {
        return Spliterators.spliterator(this, 0);
    }

    //生成当前集合的流，我们会在后面进行讲解
    default Stream<E> stream() {
        return StreamSupport.stream(spliterator(), false);
    }

    //生成当前集合的并行流，我们会在下一章多线程部分中进行介绍
    default Stream<E> parallelStream() {
        return StreamSupport.stream(spliterator(), true);
    }
```

:star: `paralleStream`就是利用了可拆分迭代器进行多线程操作，就是一个多线程执行的流，它通过默认的`ForkJoinPool`实现，它可以提高你的多线程任务的速度

:one: 下面这个使用并行流代码，使用`forEach`方法，发现输出顺序并不是按照列表里面来排的，而且输出的进程名也不一样，其实他就是开了多个线程去执行这个代码

```java
public static void main(String[] args) {
    List<Integer> list = new ArrayList<>(Arrays.asList(1, 4, 5, 2, 9, 3, 6, 0));
    list
            .parallelStream()    //获得并行流
            .forEach(i -> System.out.println(Thread.currentThread().getName()+" -> "+i)); //打印线程名字
}

//输出:
main -> 3
main -> 9
main -> 6
ForkJoinPool.commonPool-worker-2 -> 0
ForkJoinPool.commonPool-worker-11 -> 2
ForkJoinPool.commonPool-worker-9 -> 5
ForkJoinPool.commonPool-worker-4 -> 1
```

:two: 如果你想顺序输出就可以调用`forEachOrdered`方法

```java
public static void main(String[] args) {
    List<Integer> list = new ArrayList<>(Arrays.asList(1, 4, 5, 2, 9, 3, 6, 0));
    list
            .parallelStream()    //获得并行流
            .forEachOrdered(System.out::println);
}
```

:star: 在`Arrays`数组工具类中，也包含了很多`parallel`方法

| 方法名                                                       | 描述                                                         | 使用示例                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `parallelSort(int[] a)`                                      | 并行地对整型数组进行排序。                                   | ```java\nint[] arr = {2, 5, 1, 7, 6};\nArrays.parallelSort(arr);\nsystem.out.println(Arrays.toString(arr));\n``` |
| `parallelSort(int[] a, int fromIndex, int toIndex)`          | 并行地对整型数组的一部分进行排序（从`fromIndex`到`toIndex`-1）。 | ```java\nint[] arr = {2, 5, 1, 7, 6};\nArrays.parallelSort(arr, 1, 4);\nsystem.out.println(Arrays.toString(arr));\n``` |
| `parallelSort(long[] a)`                                     | 并行地对长整型数组进行排序。                                 | ```java\nlong[] arr = {2L, 5L, 1L, 7L, 6L};\nArrays.parallelSort(arr);\nsystem.out.println(Arrays.toString(arr));\n``` |
| `parallelSort(long[] a, int fromIndex, int toIndex)`         | 并行地对长整型数组的一部分进行排序。                         | ```java\nlong[] arr = {2L, 5L, 1L, 7L, 6L};\nArrays.parallelSort(arr, 1, 4);\nsystem.out.println(Arrays.toString(arr));\n``` |
| `parallelSort(double[] a)`                                   | 并行地对双精度浮点数数组进行排序。                           | ```java\ndouble[] arr = {2.2, 5.5, 1.1, 7.7, 6.6};\nArrays.parallelSort(arr);\nsystem.out.println(Arrays.toString(arr));\n``` |
| `parallelSort(double[] a, int fromIndex, int toIndex)`       | 并行地对双精度浮点数数组的一部分进行排序。                   | ```java\ndouble[] arr = {2.2, 5.5, 1.1, 7.7, 6.6};\nArrays.parallelSort(arr, 1, 4);\nsystem.out.println(Arrays.toString(arr));\n``` |
| `parallelSort(Object[] a)`                                   | 并行地对对象数组进行排序，要求数组元素实现`Comparable`接口。 | ```java\nString[] arr = {"banana", "apple", "cherry"};\nArrays.parallelSort(arr);\nsystem.out.println(Arrays.toString(arr));\n``` |
| `parallelSort(Object[] a, int fromIndex, int toIndex)`       | 并行地对对象数组的一部分进行排序。                           | ```java\nString[] arr = {"banana", "apple", "cherry"};\nArrays.parallelSort(arr, 0, 2);\nsystem.out.println(Arrays.toString(arr));\n``` |
| `parallelSort(T[] a, Comparator<? super T> cmp)`             | 并行地对具有指定比较器的对象数组进行排序。                   | ```java\nString[] arr = {"banana", "apple", "cherry"};\nArrays.parallelSort(arr, String.CASE_INSENSITIVE_ORDER);\nsystem.out.println(Arrays.toString(arr));\n``` |
| `parallelSort(T[] a, int fromIndex, int toIndex, Comparator<? super T> cmp)` | 并行地对对象数组的一部分进行排序，使用指定的比较器。         | ```java\nString[] arr = {"Banana", "apple", "Cherry"};\nArrays.parallelSort(arr, 0, 3, String.CASE_INSENSITIVE_ORDER);\nsystem.out.println(Arrays.toString(arr));\n``` |

```java
    public static void main(String[] args) {
//        List<Integer> list = new ArrayList<>(Arrays.asList(1, 4, 5, 2, 9, 3, 6, 0));
        int[] arr = new int[]{1,2,3,5,6,7,0,8};
        Arrays.parallelSort(arr);
        System.out.println(Arrays.toString(arr));
    }
```

:star: 学习了线程之后，之前学习的线程可能会有点不适用了，因为在线程中可能要对同一个对象来进行操作，之前学习的集合类都是基于单线程设计的，如果同时操作这个对象，就会出现异步的问题

:one: 两个线程都操纵一个`List`，得到的结果往往不是`30000`，而且还可能会报错

```java
    public static void main(String[] args) throws InterruptedException {
        List<Integer> list = new ArrayList<>();
        new Thread(() -> {
            for (int i = 0; i < 10000; i++) {
                list.add(i);   //两个线程同时操作集合类进行插入操作
            }
        }).start();
        new Thread(() -> {
            for (int i = 1000; i < 20000; i++) {
                list.add(i);
            }
        }).start();
        Thread.sleep(2000);
        System.out.println(list.size());
    }
```

![image-20240426140953982](https://image-1305907375.cos.ap-chengdu.myqcloud.com/Flower_bound_manage_boundimage-20240426140953982.png)

:two: 报错原因是，因为之前的集合类，并没有考虑到多线程运行的情况，如果两个线程同时执行，那么有可能两个线程同一时间都执行同一个方法，这种情况下就很容易出问题

```java
public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // 当数组容量更好还差一个满的时候，这个时候两个线程同时走到了这里，因为都判断为没满，所以说没有进行扩容，但是实际上两个线程都要插入一个元素进来
    elementData[size++] = e;   //当两个线程同时在这里插入元素，直接导致越界访问
    return true;
}
```

:star: 在`Java`早期，还有一些老的集合，它们都是线程安全的，因为它们在每个方法都加了个锁，但是这样会导致运行速度变慢，开销更大，但是它们都不会在使用了

```java
public static void main(String[] args) throws InterruptedException {
    Vector<Integer> list = new Vector<>();   //我们可以使用Vector代替List使用
  	//Hashtable<Integer, String>   也可以使用Hashtable来代替Map
    new Thread(() -> {
        for (int i = 0; i < 1000; i++) {
            list.add(i);
        }
    }).start();
    new Thread(() -> {
        for (int i = 1000; i < 2000; i++) {
            list.add(i);
        }
    }).start();

    Thread.sleep(1000);
    System.out.println(list.size());
}
```

:one: 它们的一些源代码

```java
    public synchronized void copyInto(Object[] anArray) {
        System.arraycopy(elementData, 0, anArray, 0, elementCount);
    }

    /**
     * Trims the capacity of this vector to be the vector's current
     * size. If the capacity of this vector is larger than its current
     * size, then the capacity is changed to equal the size by replacing
     * its internal data array, kept in the field {@code elementData},
     * with a smaller one. An application can use this operation to
     * minimize the storage of a vector.
     */
    public synchronized void trimToSize() {
        modCount++;
        int oldCapacity = elementData.length;
        if (elementCount < oldCapacity) {
            elementData = Arrays.copyOf(elementData, elementCount);
        }
    }

    /**
     * Increases the capacity of this vector, if necessary, to ensure
     * that it can hold at least the number of components specified by
     * the minimum capacity argument.
     *
     * <p>If the current capacity of this vector is less than
     * {@code minCapacity}, then its capacity is increased by replacing its
     * internal data array, kept in the field {@code elementData}, with a
     * larger one.  The size of the new data array will be the old size plus
     * {@code capacityIncrement}, unless the value of
     * {@code capacityIncrement} is less than or equal to zero, in which case
     * the new capacity will be twice the old capacity; but if this new size
     * is still smaller than {@code minCapacity}, then the new capacity will
     * be {@code minCapacity}.
     *
     * @param minCapacity the desired minimum capacity
     */
    public synchronized void ensureCapacity(int minCapacity) {
        if (minCapacity > 0) {
            modCount++;
            ensureCapacityHelper(minCapacity);
        }
    }
```

:star: 在`Java.util.concurrent`有很多专门用于并发操作的集合类

![image-20240426141421980](https://image-1305907375.cos.ap-chengdu.myqcloud.com/Flower_bound_manage_boundimage-20240426141421980.png)



