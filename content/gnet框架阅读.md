---
title: "Gnet框架阅读"
date: 2024-04-17T17:29:33+08:00
draft: true
categories:
    -
tags:
    -
---

# Gnet

## Gnet简介

## Reactor模型

### 主从多Reactor模型与事件循环
关于使用事件循环的框架中最出名的当属Node.js，Node采用事件循环+libuv实现了单线程下的异步，是的无需考虑多线程与并发也能实现较高的吞吐量，但是Node的单线程同样也限制了Node无法更好的利用当下设备的多处理器（多核心）能力，造成资源的浪费，于是netty提出了主从多Reactor模型，采用多个Reactor事件循环加速，充分结合I/O多路复用与多线程的多核心并发能力，进一步逼近及其的性能极限。

`事件循环`：事件循环本身与I/O多路复用密切集合，采用操作系统提供的IO事件接口来实现用户态的单线程IO事件管理，通过向操作系统注册IO对象以及对IO对象感兴趣的事件和事件对应的回调函数，这样无需阻塞主线程也能实现IO事件的处理。

`主从多Reactor`：一种高级的事件循环，采用1（MainReactor）+N（SubReacter）的模式的多线程或多进程（NGINX）来改进事件循环的不能利用CPU多核并行的问题。
## Gnet核心

### Engine
```go
type engine struct {
	ln         *listener    // the listener for accepting new connections 底层网络监听器
	opts       *Options     // options with engine
	acceptor   *eventloop   // main event-loop for accepting connections 主事件循环用于接受连接
	eventLoops loadBalancer // event-loops for handling events
	inShutdown int32        // whether the engine is in shutdown
	ticker     struct {
		ctx    context.Context    // context for ticker
		cancel context.CancelFunc // function to stop the ticker
	}
	workerPool struct {
		*errgroup.Group

		shutdownCtx context.Context
		shutdown    context.CancelFunc
		once        sync.Once
	}
	eventHandler EventHandler // user eventHandler
}
```
Engine是Gnet的顶层结构，负责管理gnet服务，根据结构体我们可以看到主要的包含accepor用于监听socket号，eventLoops主要是基于某种负载均衡算法事件循环,然后就是一个wokerPool也就是线程池。其余都是些小组件吗，包括listener，选项加载器，用户事件钩子函数，用于

### EventLoop
```go 
type eventloop struct {
	ln           *listener       // listener 网络监听器
	idx          int             // loop index in the engine loops list 事件循环在引擎循环列表中的索引
	cache        bytes.Buffer    // temporary buffer for scattered bytes 临时缓冲区
	engine       *engine         // engine in loop 		引擎
	poller       *netpoll.Poller // epoll or kqueue 	轮询器，在不同的操作系统中有不同的实现，如Linux_epoll或MacOS_kqueue
	buffer       []byte          // read packet buffer whose capacity is set by user, default value is 64KB 读取数据包缓冲区，其容量由用户设置，默认值为64KB
	connections  connMatrix      // loop connections storage 	事件循环中的网络连接实例的存储
	eventHandler EventHandler    // user eventHandler 用户事件处理程序
}
```
#### MainReactor
主Reactor模型，主要是一个eventloop，Polling主要接收一个accept函数来实现对socket的监听。
run函数负责启动gnet框架，根据事件句柄，协议地址和可选参数进行启动，run函数主要负责加载可选选项并启动模块。

配置事件循环数量，同时配置context并根据配置决定是否采用多个事件循环来充分利用CPU来进行处理。

对于多个事件循环，默认自带三个方法进行负载均衡：`轮询`，`最少连接数`，`源地址哈希`
开启了滴答器的话，同时配置context。
启动钩子函数engine的OnBoot方法，根据用户自己来决定是否实现，同时启动从reactor

#### SubReactor
子Reactor模型，与MainReactor类似，也是一个eventloop，Polling主要监听来自Connection的IO事件。

### Poller
```go 
type Poller struct {
	fd                          int    // epoll fd
	efd                         int    // eventfd
	efdBuf                      []byte // efd buffer to read an 8-byte integer 用于读取8字节整数的efd缓冲区
	wakeupCall                  int32
	asyncTaskQueue              queue.AsyncTaskQueue // queue with low priority 低优先级队列
	urgentAsyncTaskQueue        queue.AsyncTaskQueue // queue with high priority 高优先级队列
	highPriorityEventsThreshold int32                // threshold of high-priority events
}
```
Poller是eventloop的核心，主要是一个阻塞循环，循环内负责与内核多路复用器交流，将Task任务通过优先级队列进行发送
### WorkerPool

协程池，从协程池挑选协程对事件循环中得到的Connection进行处理，防止阻塞eventloop主协程
















## 技巧笔记
- 高阶函数闭包实现过滤器
```go
func filter() bool{

}

func test(f func() bool){
    if f() {

    }
}

func main(){
    test(filter())
}
```
- 高阶函数迭代器
```go
func (r *RawList) iterate(i func() bool){
    for _,el:=range r
        if !i(){
            break
        }
}

iterate(func() bool{
    // to do
    return true
})
```
- CAS无锁队列
  
  利用CAS锁来实现高性能无锁队列，队列是两个线程交互的临界区，可能出现多个线程读写队列，可能造成读旧数据和数据写入失效。举例：两个线程一个写（A）一个读（B），A创建完新节点但是为进行数据写入，操作系统发生调度时间片产生中断，C线程来读，可能发生意外事件。为什么要采用CAS？队列的Node节点操作都十分迅速，在系统中Node节点资源会快速以异步协程的方式分发给workerpool，不会阻塞事件循环的操作线程，速度极快，CAS正适合，重量级锁需要系统的调度。调度算法参考下一个文章