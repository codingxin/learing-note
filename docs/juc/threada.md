# 一.线程池


**线程池相关类**

> 1. Excutor: 执行者 – 顶层接口

void execute(Runnable command); 执行可运行的任务


- submit 方法 -> 有返回值，用 Future 封装

- execute 方法 -> 无返回值

- submit 方法抛异常可以在主线程中 catch 到

- execute 方法执行任务是捕捉不到异常的


> 2. ExcutorService: 接口 API

重要方法 说明
- void execute(Runnable command); 执行可运行的任务
- void shutdown(); 关闭线程池
- List<Runnable> shutdownNow(); 立即关闭
- Future<?> submit(Runnable task); 提交任务; 允许获取执行结果
- <T> Future<T> submit(Runnable task, T result); 提交任务（指定结果）; 控制|获取执行结果
- <T> Future<T> submit(Callable<T> task); 提交任务; 允许控制任务和获取执行结果

区别：
- shutdown()：停止接收新任务，原来的任务继续执行
- shutdownNow()：停止接收新任务，原来的任务停止执行
- awaitTermination(long timeOut, TimeUnit unit)：当前线程阻塞

> 3. ThreadFactory: 线程工厂






> 4. Executors: 工具类


**ThreadPoolExecutor重要属性和方法**

- int corePoolSize; 核心线程数
- int maximumPoolSize; 最大线程数
- ThreadFactory threadFactory; 线程创建工厂
- BlockingQueue<Runnable> workQueue; 工作队列
- RejectedExecutionHandler handler; 拒绝策略处理器
- void execute(Runnable command) 执行
- Future<?> submit(Runnable task) 提交任务
- submit(Runnable task, T result) 提交任务
- submit(Callable<T> task) 提交任务


**线程池参数**

> 核心线程数
> 最大线程数
> 空闲线程存活时间
> 空闲线程存活时间单位
> 缓冲队列
> 线程工厂
> 饱和拒绝策略



1. 缓冲队列

BlockingQueue 是双缓冲队列。BlockingQueue 内部使用两条队列，允许两个线程同时向队列一个存储，一个取出操作。在保证并发安全的同时，提高了队列的存取效率

- ArrayBlockingQueue:规定大小的 BlockingQueue，其构造必须指定大小。其所含的对象是 FIFO 顺序排序的
- LinkedBlockingQueue:大小不固定的 BlockingQueue，若其构造时指定大小，生成的 BlockingQueue 有大小限制，不指定大小，其大小有 Integer.MAX_VALUE 来决定。其所含的对象是 FIFO 顺序排的。
- PriorityBlockingQueue:类似于 LinkedBlockingQueue，但是其所含对象的排序不是 FIFO，而是依据对象的自然顺序或者构造函数的 Comparator 决定。
- SynchronizedQueue:特殊的 BlockingQueue，对其的操作必须是放和取交替完成


2. 拒绝策略

- ThreadPoolExecutor.AbortPolicy:丢弃任务并抛出 RejectedExecutionException异常 (默认)
- 
- ThreadPoolExecutor.DiscardPolicy：丢弃任务，但是不抛出异常。
- 
- ThreadPoolExecutor.DiscardOldestPolicy：丢弃队列最前面的任务，然后重新提交被拒绝的任务

- ThreadPoolExecutor.CallerRunsPolicy：由调用线程（提交任务的线程）处理该任务



3. 线程工厂

实现ThreadFactory，设置线程的属性 （如线程名字）




**线程池执行流程**
1. 先判断核心线程数是否已满
2. 如果满了 将任务添加进等待队列
3. 如果等待队列满了 新建线程 执行任务
4. 如果线程数达到最大线程数，执行饱和拒绝策略



**默认的线程池实现**

> 1. newSingleThreadExecutor

创建一个单线程的线程池。这个线程池只有一个线程在工作，也就是相当于单线程串行执行所有任务。如果这个唯一的线程因为异常结束，那么会有一个新的线程来替代它。此线程池保证所有任务的执行顺序按照任务的提交顺序执行。

> 2.newFixedThreadPool

创建固定大小的线程池。每次提交一个任务就创建一个线程，直到线程达到线程池的最大大小。线程池的大小一旦达到最大值就会保持不变，如果某个线程因为执行异常而结束，那么线程池会补充一个新线程。

> 3. newCachedThreadPool

创建一个可缓存的线程池。如果线程池的大小超过了处理任务所需要的线程，那么就会回收部分空闲（60秒不执行任务）的线程，当任务数增加时，此线程池又可以智能的添 加新线程来处理任务。此线程池不会对线程池大小做限制，线程池大小完全依赖于操作系统（或者说JVM）能够创建的最大线程大小。

> 4.newScheduledThreadPool

创建一个大小无限的线程池。此线程池支持定时以及周期性执行任务的需求。



> Runnable#run()没有返回值
> Callable#call()方法有返回值


**Future**

- boolean cancel(boolean mayInterruptIfRunning);取消任务执行时是否打断）
- boolean isCancelled(); 是否被取消
- boolean isDone(); 是否执行完毕
- V get() throws InterruptedException, ExecutionException;获取执行结果
- V get(long timeout, TimeUnit unit)throws InterruptedException, ExecutionException, TimeoutException;限时获取执行结果