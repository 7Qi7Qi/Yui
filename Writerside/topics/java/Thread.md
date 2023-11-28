# 线程

## 线程和进程
1. 进程是操作系统资源分配的基本单位
2. 线程是处理器任务调度和执行的基本单位

## 线程池

### UML
![java.util.concurrent.Executor](Executor.png)

### 参数

1. corePoolSize 核心线程大小
2. maximumPoolSize 最大线程数量
3. workQueue 队列长度

> 创建线程时，如果当前线程数量没有到corePoolSize，则会创建线程；核心线程空闲也不会被销毁。
> 当达到corePoolSize，则会进入workQueue；
> 当队列已满，则会创建线程直至maximumPoolSize

### 线程池的两种提交任务方式区别
+ execute只能提交Runnable，无返回值
  + ThreadPoolExecutor.java
+ submit既可以提交Runnable，返回值null；也可以提交Callable，返回值Future
  + AbstractExecutorService.java
  + Callable是设置如何产生结果，Future用来获取结果
+ 执行遇到异常
  + execute 会直接抛出，可以通过Thread::setUncaughtExceptionHandler来处理
  + submit 不会直接抛出，需要使用Future.get()回去返回值时才抛出。

### Executors

#### 类型

1. SingleThreadExecutor：单线程的线程池
2. FixedThreadPool：固定大小的线程池
3. CachedThreadPool：核心线程数为0，可以服用之前创建但现在空闲的线程的线程池
4. ScheduledThreadPool：支持定时及周期性执行任务的线程池
5. WorkingStealingPool：java1.8之后，自动调用系统可用的线程数，并行执行任务

#### 弊端

1. ``FixedThreadPool``和``SingleThreadPool``：允许请求的队列长度为Integer.MAX_VALUE，可能会堆积大量请求，导致oom
2. ``CachedThreadPool``：允许创建的线程数量为Integer.MAX_VALUE，可能 创建大量线程，导致oom

> 推荐通过``ThreadPoolExecutor``方式来实现线程池

### ThreadPoolExecutor

#### execute方法

>  Proceed in 3 steps:
>
>  1. If fewer than corePoolSize threads are running, try to
>  start a new thread with the given command as its first
>   task.  The call to addWorker atomically checks runState and
>   workerCount, and so prevents false alarms that would add
>   threads when it shouldn't, by returning false.
>
>  2. If a task can be successfully queued, then we still need
>  to double-check whether we should have added a thread
>   (because existing ones died since last checking) or that
>   the pool shut down since entry into this method. So we
>   recheck state and if necessary roll back the enqueuing if
>   stopped, or start a new thread if there are none.
>
>  3. If we cannot queue task, then we try to add a new
>  thread.  If it fails, we know we are shut down or saturated
>   and so reject the task.

#### 拒绝策略

+ RejectedExecutionHandler，任务处理不过来时，拒绝策略

```java
import java.util.concurrent.ThreadPoolExecutor;

public interface RejectedExecutionHandler {
    void rejectedExecution(Runnable r, ThreadPoolExecutor executor);
}
```

1. new ThreadPoolExecutor.AbortPolicy()
   1. 直接抛异常
2. new ThreadPoolExecutor.CallerRunsPolicy()
   1. 线程池如果未关闭，直接执行run方法
3. new ThreadPoolExecutor.DiscardPolicy()
   1. 新任务直接丢弃
4. new ThreadPoolExecutor.DiscardOldestPolicy()
   1. 线程池等待任务移除队列头部元素（poll()方法），将新任务加到队列中
5. 自定义策略，比如持久化多余的线程，用定时任务把持久化线程取出

## Thread

### sleep方法和wait方法区别
1. 所属类和方法类型不同：Thread.sleep() ; new Object().wait()
2. 使用语法不同：
   1. sleep可以直接使用
   2. wait方法需要先拿到锁，才可以调object.wait()
3. 唤醒方式不同
   1. sleep 需要指定时间
   2. wait 可以传时间，也可以不传时间；或者使用notify() /notifyAll()
4. 释放锁资源不同
   1. sleep 休眠时不会释放锁资源
   2. wait 休眠时会释放锁资源
5. 线程状态不同
   1. sleep: TIMED_WAITING
   2. wait: WAITING