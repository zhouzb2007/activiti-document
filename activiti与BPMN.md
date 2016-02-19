### 1. BPMN概述
> 由BPMI(The Business Process Management Initiative)开发了一套标准叫业务流程建模符号(BPMN)。  
>
> BPMN的主要目标是提供一些被所有业务用户容易理解的符号，业务人员负责流程的分析创建，技术人员负责流程的执行，最后业务人员将监控流程的执行。因此BPMN创造了技术和业务交流之间的桥梁。  

**BPMN主要包括基本元素**  
　　Flow Objects(流对象)  
　　Data(数据)  
　　Connecting Objects(连接对象)  
　　Swimlanes(甬道)  
　　Artifacts(描述对象)  


### 2. 流对象
有三个流对象 Event, Activity, Gateway  

**Event,事件**  
用一个圆圈代表事件(Event)，它指的是在业务流程的运行过程中发生的事情。这些事件影响流程的流转，事件通常有一个cause (trigger)或者impact (result)。

有三种类型的事件：Start, Intermediate, and End

**Activity,活动**  
用圆角矩形表示一个活动，一个活动可以由多个活动组成，活动的类型包括：Task 和 Sub-Process

**Gateway,网关**  
菱形代表网关，用来控制流程的分支和聚合  
![activiti流对象](http://7xphqb.com1.z0.glb.clouddn.com/b0a722423e4a327574b7f283e0268436.png)

### 3. 连接对象
在一个图中，连接对象将流程对象连接起来组成业务流程的结构  

**Sequence Flow 序列流**  
用实线实心箭头表示，代表流程中将被执行的活动的执行顺序

**Message Flow 消息流**  
用虚线空心箭头表示，用来表示2个分开的流程参与者（业务实体或业务角色）之间发送或者接收到的消息流

**Association 结合关系**  
点状虚线表示，用于显示活动的输入输出  
![activiti连接对象](http://7xphqb.com1.z0.glb.clouddn.com/aa3514693de8d53a867663a520b24ec7.png)

### 4. 甬道
用以区分不同的功能和职责

**Pool**  
代表流程中的一个参与者。它也可以用作一个图形容器来与其他的pool相分隔  
![activiti甬道](http://7xphqb.com1.z0.glb.clouddn.com/b6a87283d8e0afb3578cb72b3a9bbac4.png)

Pool主要用于多个个独立的实体或者参与者之间的物理划分，如下图就是一个带pool的业务流程图
![activiti甬道pool](http://7xphqb.com1.z0.glb.clouddn.com/67370f1d5c1232a553bb3775a38bbfe4.png)

### 5. 描述对象
### 6. BPMN的用途
### 7. BPMN的使用价值
