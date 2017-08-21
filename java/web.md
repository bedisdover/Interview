# 5.8 Web 相关

### Servlet 是线程安全的吗？

Servlet 不是线程安全的。当 Web 容器接收到 Client 的请求时，容器从线程池中取出一个线程，之后若目标 Servlet 不存在，则进行实例化，若已存在，则不再实例化，之后调用 `service()` 方法。因此每一个 Servlet 对象在容器中只有一个实例对象，即是单例模式。

如果多个请求请求的是同一个 Servlet，那么请求对应的线程将并发调用 Servlet 的 `service()` 方法。若 Servlet 中定义了实例变量或静态变量，那么可能会发生线程安全问题。

### Spring AOP

AOP（Aspect Orient Programming），一般称为面向切面编程，作为面向对象的一种补充，用于处理系统中分布于各个模块的横切关注点，比如事务管理、日志、缓存等等。

Spring AOP 使用动态代理，在内存中临时为对象生成一个 AOP 对象，这个 AOP 对象包含了目标对象的全部方法，并且在特定的切点做了增强处理，并回调原对象的方法。

Spring AOP中的动态代理主要有两种方式，JDK 动态代理和 CGLIB 动态代理。JDK 动态代理通过反射来接收被代理的类，并且要求被代理的类必须实现一个接口。JDK动态代理的核心是 InvocationHandler 接口和 Proxy 类。如果目标类没有实现接口，那么 Spring AOP 会选择使用CGLIB来动态代理目标类。

> 详细可参考
>
> * [Spring 容器AOP的实现原理——动态代理](http://wiki.jikexueyuan.com/project/ssh-noob-learning/dynamic-proxy.html)
> * [代理模式](http://design-patterns.readthedocs.io/zh_CN/latest/structural_patterns/proxy.html)


### Spring IOC

IOC（Inversion of Control），一般称为控制反转，通过引入 IOC 容器，利用依赖关系注入的方式，实现对象之间的解耦。