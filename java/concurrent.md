# 5.3 并发和多线程

## synchronized

synchronized 是 java 中的关键字，可以作用于代码块、方法、静态方法、类上，实现同步锁。synchronized 不能继承。


### synchronized 和 lock 的区别

- synchronized 是 JVM 层面实现的，代码执行时出现异常，JVM 会自动释放锁定
- lock 是 java 代码实现的，要保证锁一定被释放，必须把 unlock() 放到 finally 块中

- synchronized 原始采用的是 CPU 悲观锁机制
- lock 采用乐观锁机制

- 在资源竞争不是很激烈的情况下，synchronized 的性能要优于 ReentrantLock
- 在资源竞争很激烈的情况下，ReentrantLock 的性能优于 synchronized

线程 A 和 B 都要获取对象 o 的锁定，假设 A 获取了对象 o 锁

- 使用 synchronized, 如果 A 不释放，B 将一直等下去，不能被中断
- 使用 ReentrantLock，如果 A 不释放，可以使 B 在等待了足够长的时间以后，中断等待

ReectrantLock 获取锁的方式

- lock(), 如果获取了锁立即返回，如果别的线程持有锁，当前线程则一直处于休眠状态，直到获取锁
- tryLock(), 如果获取了锁立即返回true，如果别的线程正持有锁，立即返回false
- tryLock(long timeout, TimeUnit unit)， 如果获取了锁定立即返回true，如果别的线程正持有锁，会等待参数给定的时间，在等待的过程中，如果获取了锁定，就返回true，如果等待超时，返回false
- lockInterruptibly(), 如果获取了锁定立即返回，如果没有获取锁定，当前线程处于休眠状态，直到或者锁定，或者当前线程被别的线程中断


### synchronized 和 static synchronized 的区别

- synchronized 是对类的当前实例进行加锁，防止其他线程同时访问该类的该实例的所有 synchronized 块，注意这里是“类的当前实例”， 类的两个不同实例就没有这种约束了。
- static synchronized 控制类的所有实例的访问，限制线程同时访问 jvm 中该类的所有实例同时访问对应的代码快。


## volatile

- 保证变量的可见性，即一个线程修改了某个变量的值，新值对其他线程来说是立即可见的
- 禁止进行指令重排序


## AtomicInteger

语句 i++ 不是原子操作，可以使用 AtomicInteger 保证原子性


### java 线程的状态


### java 如何创建线程

- 继承 Thread 类，并重写 run() 方法，调用 start() 方法启动线程
- 实现 Runnable 接口，并重写 run() 方法，调用 new Thread(Runnable target).start() 方法启动线程

区别：

- 继承 Thread 类，无法继承其他父类；访问当前线程直接使用 this
- 实现 Runnable 接口，可以继承其他父类；访问当前线程需使用 Thread.currentThread() 方法


### run() 和 start() 的区别

- run() 方法只是类的一个普通方法而已，如果直接调用 run() 方法，程序中依然只有主线程这一个线程，其程序执行路径还是只有一条，还是要顺序执行，run() 方法体执行完毕后才可继续执行下面的代码
- start() 方法无需等待 run() 方法体代码执行完毕而直接继续执行下面的代码。通过调用 start() 方法来启动一个线程，这时此线程处于就绪（可运行）状态，并没有运行，一旦得到 cpu 时间片，就开始执行 run() 方法，run() 方法运行结束，此线程随即终止


### Thread.sleep() 和 wait() 的区别
