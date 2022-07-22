#### 1.为什么主线程不会因为`Looper.loop() `里的死循环卡死？

> 主线程确实是通过` Looper.loop()` 进入循环状态，因为这样主线程才不会像我们一般创建的线程一样，当可执行代码执行完后，线程生命周期就终止了。
>
> 在主线程的`MessageQueue`没有消息时，便阻塞在 `MessageQueue.next `中的 `nativePollOnce()`方法里，此时主线程会释放CPU 资源进入休眠状态，直到新消息达到。所以主线程大多数都是出于休眠状态，并不会消耗大量CPU资源。
>
> 这里采用的linux的epoll机制，是一种IO多路复用机制，可以同时监控多个文件描述符，当某个文件描述符就绪（读或写就绪），则立刻通知相应程序进行读或写操作拿到最新消息，进而唤醒等待的线程。

#### 2.post和sendMessage两类发送消息的方法有什么区别？

> `post `类的方法发送的是`Runnable`对象，但是其最后还是会被封装成`Message`对象，将`Runnable`对象赋值给`Message`对象中的`callback`变量，然后交由`sendMessageAtTime()`方法发送出去。在处理消息时，会在`dispatchMessage()`方法里首先被` handleCallback(msg)`发放执行，实际就是执行`Message`对象里面的`Runnable`对象的 `run` 方法。
>
> 而`sendMessage`类的方法发送的直接是Message对象，处理消息时，在`dispatchMessage`里优先级会低于`handleCallback(msg)`方法，是通过自己重写的`handleMessage(msg)`发放执行。

#### 3.为什么要通过`Message.obtain()`方法获取Message对象？

> `obtain`方法可以从全局消息池中得到一个空的Message对象，这样可以有效节省系统资源。同时，通过各种obtain重载方法还可以得到一些Message的拷贝，或对Message对象进行一些初始化。

#### 4.Handler实现发送延迟消息的原理是什么？

> 我们常用`postDelayed`与`sendMessageDelayed`来发送延迟消息，其实最终都是将延迟时间转换为确定时间，然后通过`sendMessageAtTime() -> enqueueMessage -> queue.enqueueMessage`这一系类方法将消息插入到MessageQueue中。所以并不是先延迟再发送消息，而是直接发送消息，在借助MessageQueue的设计来实现消息的延迟处理。
>
> 消息延迟处理的原理涉及`MessageQueue`的两个静态方法`MessageQueue.next()`和`MessageQueue.enqueueMessage()`。通过Native方法阻塞线程一定时间，等到消息的执行时间到后再取出消息执行

#### 5.同步屏障`SyncBarrier`是什么？有什么作用？

> 在一般情况下，同步和异步消息处理起来没有什么不同。只有设置了同步屏障后才会有差异。同步屏障从代码层面上看是一个`Message`对象，但是其`target`属性为`null`，用以区分普通消息。在`MessageQueue.next()`中如果当前消息是一个同步屏障，则跳过后面所有的同步消息，找到第一个异步消息来处理。但是开发者调用不了。在ViewRootImp的UI测绘流程有体现

#### 6.`IdleHandler`是什么？有什么作用？

> 当消息队列没有消息时调用或者如果队列中仍有待处理的消息，但都未到执行时间时，也会调用此方法。用以监听主线程空闲状态。

#### 7.为什么非静态的`Handler`导致内存泄露？如果解决？

> 首先，非静态的内部类、匿名内部类、局部内部类都会隐式的持有其外部类的引用。也就是说在`Activity`中创建的`Handler`会因此持有`Activity`的引用。
>
> 当我们在主线程使用`Handler`的时候，`Handler`会默认绑定这个线程的`Looper`对象，并关联其`MessageQueue`，`Handler`发出的所有消息都会加入到这个`MessageQueue`中.`Looper`对象的生命中期贯穿整个主线程的生命周期，所以当`Looper`对象中的`MessageQueue`里还有未处理完的`Message`时，因为每个`Message`都持有`Handler`的引用，所以`Handler`无法被回收，自然其持有引用的外部类`Activity`也无法回收，造成泄漏。
>
> **使用静态内部类+弱引用的方式**

#### 8.如何让在子线程中弹出toast

> 调用`Looper.prepare`以及`Looper.loop()`，但是切记线程任务执行完，需要手动调用`Looper.quitSafely()`否则线程不会结束，