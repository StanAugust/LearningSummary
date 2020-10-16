# 原理方面的基础知识

## 一、NIO (Non-Block IO)

非阻塞IO

### 1. Channel

​	NIO中，一个连接用一个通道（channel）来表示

### 2. Selector

​	一个选择器（selector）可以监控很多通道

​	通道和选择器之间的关系：

  + 将通道实例注册（register）到一个选择器中

  + 注册完之后，可以调用选择器的选择方法（selector（））来选择

  + 被selector选中的IO事件叫**选择键**（SelectionKey sk）

    

## 二、Reactor反应器模式

### 1. Reactor反应器

​	查询IO并发送给Handler

​	

### 2. Handler处理器

​	处理IO事件

+ 把sk连接到handler上（sk.attach(new handler())）
+ 有事件要处理时，再调用(handler = sk.attachment())



## 三、异步回调模式

