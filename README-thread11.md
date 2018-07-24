### ThreadPoolExecutor
```
public ThreadPoolExecutor(int corePoolSize,
                          int maximumPoolSize,
                          long keepAliveTime,
                          TimeUnit unit,
                          BlockingQueue<Runnable> workQueue,
                          ThreadFactory threadFactory,
                          RejectedExecutionHandler handler) {}
```
#### 参数说明

|参数|说明|
|------|------|
|corePoolSize|核心线程数；默认情况下线程池时空的，只有任务提交时才会创建线程；如果当前运行的线程少于corePoolSize则创建新线程来处理任务，如果等于或多于corePoolSize则不再创建|
|maximumPoolSize|线程池允许创建的最大线程数；如果任务队列满了并且线程数小于maximumPoolSize时，则线程池仍旧会创建新的线程来处理任务|
|keepAliveTime|非核心线程闲置的超时时间，超过这个时间则回收|
|unit|keepAliveTime参数的时间单位|
|workQueue|任务队列，如果当前线程数大于corePoolSize，则将任务添加到此任务队列中|
|threadFactory|线程工厂|
|handler|饱和策略，当任务队列和线程池都满了时所采用的应对策略，默认为AbordPolicy表示无法处理新任务，抛出异常|
