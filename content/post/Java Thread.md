---
title: Java多线程
date: 2021-11-03 9:21:30
categories:
- 书籍笔记
tags:
- Java
---
# Java 多线程

## 一.线程实现

线程的三种实现

1. 继承Thread类

   > 不推荐使用继承Thread类，应为java只能单继承，如果要继承其他类则无法实现

   ```java
   public class ThreadTest extends Thread{
       public static void main(String[] args) {
           new ThreadTest().start();
       }
       @Override
       public void run() {
           System.out.println("fuck you");
       }
   }
   ```

   

2. 实现Runnable接口

   > 推荐使用Runnable接口，实现Runnable接口可以继承其他类，避免局限性

   ```java
   public class RunnableTest implements Runnable{
       public static void main(String[] args) {
           new Thread(new RunnableTest()).start();
       }
       @Override
       public void run() {
           System.out.println("hello you");
       }
   }
   ```

   

3. 实现Callable接口

   > Callable接口需要依赖FutureTask类，FutureTask类实现了Runnable接口，做为参数传入线程主体，当线程执行完成后的返回值保存在FutureTask类中使用get方法获取返回值

   ```java
   public class CallableTest implements Callable<String> {
       @Override
       public String  call() throws Exception {
           String a="this is ";
           String b="Callable";
           return a+b;
       }
   }
   
   public static void main(String[] args) {
           CallableTest test=new CallableTest();
           FutureTask<String> futureTask= new FutureTask<>(test);
           new Thread(futureTask).start();
           try {
               System.out.println(futureTask.get());
           } catch (InterruptedException | ExecutionException e) {
               e.printStackTrace();
           }
   }
   ```

## 二.Lamda表达式

表达式格式

```java
(params)->expression[表达式]
(params)->statement[语句]
(params)->{statements}
```

Lamda线程创建

```java
new Thread(()->System.out.println("hello,this is Lamda")).start
```

### 函数式接口(Function Interface)

> 任何接口如果只包含唯一一个抽象方法，那么他就是一个函数式接口

```java
public interface Runnable(){
    public abstract void run();
}
```

## 三.线程状态

![线程状态](https://www.runoob.com/wp-content/uploads/2014/09/716271-20170320112245721-1831918220.jpg)

- 创建(new)
- 就绪(Runnable)
- 阻塞(Blocked)
- 运行(Running)
- 死亡(Dead)

### 线程方法

| 方法                           | 说明                                       |
| ------------------------------ | ------------------------------------------ |
| static void sleep(long millis) | 线程休眠，休眠时间为millis(毫秒)           |
| setPriority(int newPriority)   | 更改线程优先级                             |
| void join()                    | 等待线程终止                               |
| static void yield()            | 暂停当前正在执行得到线程对象，执行其他线程 |
| void interrupt()               | 中断线程(非必要不要用)                     |
| boolean is Alive()             | 判断线程是否存活                           |

### 1.停止线程

- 不推荐jdk自己的方法(已经废弃)
- 推荐线程自己停下来
- 使用标志位变量进行终止线程运行

### 2.线程休眠

- sleep(long millis)中millis为休眠的毫秒数
- sleep有一个异常，InterruptedException
- sleep后进入就绪状态
- sleep不会释放锁

### 3.线程礼让

- 让当前执行的线程暂停但不阻塞
- 礼让是将线程状态从运行转为就绪
- 礼让不一定有效，属于CPU调度

### 4.线程合并

- 待此线程执行完成后再执行其他线程，其他线程阻塞
- 插队

### 5.线程状态

线程状态的枚举类属性

```java
public enum State {
        NEW,
    
        RUNNABLE,
    
        BLOCKED,

        WAITING,

        TIMED_WAITING,

        TERMINATED;
    }
```

### 6.线程优先级

- 优先级用数字表示范围1-10
  Thread.MIN_PRIORITY=1
  Thread.MAX_PRIORITY=1
  Thread.NORM_PRIORITY=1
- 获取优先级：getPriority()
- 设置优先级：setPriority(int a)
  ==优先级设置需要再start调度之前==

### 7.线程分类

- 用户线程
- 守护线程
  虚拟机必须确保用户线程执行完毕，但不用等待守护线程执行完毕

## 四.线程同步

- 并发：同一个对象被多个线程同时操作