# 开始事件
# 结束事件
# 任务

## Java接收任务

- **描述**  

英文描述  

A Receive Task is a simple task that waits for the arrival of a certain message. Currently, we have only implemented Java semantics for this task. When process execution arrives at a Receive Task, the process state is committed to the persistence store. This means that the process will stay in this wait state, until a specific message is received by the engine, which triggers the continuation of the process past the Receive Task.  

中文描述  

接收任务是一个简单任务，它会等待对应消息的到达。 当前，我们只实现了这个任务的java语义。 当流程达到接收任务，流程状态会保存到存储里。 意味着流程会等待在这个等待状态， 直到引擎接收了一个特定的消息， 这会触发流程穿过接收任务继续执行。  

- **图形标记**  

英文描述  

A Receive Task is visualized as a task (rounded rectangle) with a message icon in the top left corner. The message is white (a black message icon would have send semantics)


中文描述  
接收任务显示为一个任务（圆角矩形），左上角有一个消息小标记。 消息是白色的（黑色图标表示发送语义）  

![Java Receive Task](http://7xphqb.com1.z0.glb.clouddn.com/c8a692abda41129a960443ea1e5c390c.png)

- **实例**


- **代码**




## 业务规则任务  



## 事物子流程

## 包含网管

## 基于事件的网管
# 容器
# 网关

# 边界事件

## 消息边界事件

## 取消边界事件

## 补偿边界事件

## 消息捕获事件

## 补偿抛出事件  

## 空抛出事件


# 中断事件
# 注释
# 连接
