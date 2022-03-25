# threadpool-cpp

### 原理

**线程池**，简单来说就是有一堆已经创建好的线程（最大数目一定），初始时他们都处于空闲状态，当有新的任务进来，从线程池中取出一个空闲的线程处理任务，然后当任务处理完成之后，该线程被重新放回到线程池中，供其他的任务使用，当线程池中的线程都在处理任务时，就没有空闲线程供使用，此时，若有新的任务产生，只能等待线程池中有线程结束任务空闲才能执行。

**我们为什么要使用线程池呢？**

线程过多会带来调度开销，进而影响缓存局部性和整体性能。而线程池维护着多个线程，等待着监督管理者分配可并发执行的任务。这避免了在处理短时间任务时创建与销毁线程的代价。线程池不仅能够保证内核的充分利用，还能防止过分调度。可用线程数量应该取决于可用的并发处理器、处理器内核、内存、网络sockets等的数量。

![threadpool.png](./picture/threadpool.png)

### 环境及IDE

- Visual Studio 2019
- Ubuntu 7.5.0

### 项目框图

```shell
├─TaskQueue 任务队列
│  ├─TaskQueue()	任务队列构造函数
│  ├─~TaskQueue()	任务队列析构函数
│  ├─void addTask(Task<T> task)		添加任务
│  ├─void addTask(callback f, void* arg)	添加任务
│  ├─Task<T> takeTask() 取出任务
│  ├─inline size_t taskNumber()		任务数目
│  ├─pthread_mutex_t m_mutex	互斥锁
│  └─std::queue<Task<T>> m_taskQ	任务队列
├─ThreadPool 线程池
│  ├─ThreadPool()	创建线程池并初始化
│  ├─~ThreadPool()	销毁线程池
│  ├─addTask(Task<T> task)	给线程池添加任务
│  ├─getBusyNum()	获取线程池中工作的线程的个数
│  ├─getAliveNum()	获取线程池中活着的线程的个数
│  ├─static void* worker(void* arg)	工作的线程（消费者线程）任务函数
│  ├─static void* manager(void* arg)	管理者线程任务函数
│  ├─void threadExit()	单个线程退出
│  ├─TaskQueue<T>* taskQ	任务队列
│  ├─pthread_t managerID	管理者线程ID
│  ├─pthread_t* threadIDs	工作的线程ID
│  ├─pthread_mutex_t mutexPool	锁整个的线程池
│  ├─pthread_cond_t notEmpty	任务队列是否是空了
│  ├─static const int NUMBER = 2
│  └─bool shutdown	是不是要销毁线程池，销毁为1，不销毁为0
```

# Reference

- https://subingwen.cn/linux/threadpool-cpp/
- https://www.cnblogs.com/yangang92/p/5485868.html

