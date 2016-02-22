- **描述**  

英文描述

The Event-based Gateway allows to take a decision based on events. Each outgoing sequence flow of the gateway needs to be connected to an intermediate catching event. When process execution reaches an Event-based Gateway, the gateway acts like a wait state: execution is suspended. In addition, for each outgoing sequence flow, an event subscription is created.

Note the sequence flows running out of an Event-based Gateway are different from ordinary sequence flows. These sequence flows are never actually "executed". On the contrary, they allow the process engine to determine which events an execution arriving at an Event-based Gateway needs to subscribe to. The following restrictions apply:

- An Event-based Gateway must have two or more outgoing sequence flows.
- An Event-based Gateway must only be connected to elements of type intermediateCatchEvent only. (Receive Tasks after an Event-based Gateway are not supported by Activiti.)
- An intermediateCatchEvent connected to an Event-based Gateway must have a single incoming sequence flow.

中文描述

基于事件网关允许根据事件判断流向。网关的每个外出顺序流都要连接到一个中间捕获事件。 当流程到达一个基于事件网关，网关会进入等待状态：会暂停执行。 与此同时，会为每个外出顺序流创建相对的事件订阅。

注意基于事件网关的外出顺序流和普通顺序流不同。这些顺序流不会真的"执行"。 相反，它们让流程引擎去决定执行到基于事件网关的流程需要订阅哪些事件。 要考虑以下条件：

- 基于事件网关必须有两条或以上外出顺序流。
- 基于事件网关后，只能使用intermediateCatchEvent类型。 （activiti不支持基于事件网关后连接ReceiveTask。）
- 连接到基于事件网关的intermediateCatchEvent只能有一条进入顺序流。



- **图形标记**  

基于事件网关和其他BPMN网关一样显示成一个菱形， 内部包含指定图标。

![基于事件网管](http://7xphqb.com1.z0.glb.clouddn.com/127531b8144d38eb327add3cac37e31d.png)


- **实例**  

下面的流程是一个使用基于事件网关的例子。当流程执行到基于事件网关时， 流程会暂停执行。与此同时，流程实例会订阅警告信号事件，并创建一个1分钟后触发的定时器。 这会产生流程引擎为一个信号事件等待1分钟的效果。如果1分钟内发出信号，定时器就会取消，流程会沿着信号执行。 如果信号没有出现，流程会沿着定时器的方向前进，信号订阅会被取消。  

![基于事件的网管](http://7xphqb.com1.z0.glb.clouddn.com/89138c1efff44f7105b400171879aa89.png)




- **XML代码**  

xml核心代码片段

```xml
<?xml version="1.0" encoding="UTF-8"?>
<definitions xmlns="http://www.omg.org/spec/BPMN/20100524/MODEL" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:activiti="http://activiti.org/bpmn" xmlns:bpmndi="http://www.omg.org/spec/BPMN/20100524/DI" xmlns:omgdc="http://www.omg.org/spec/DD/20100524/DC" xmlns:omgdi="http://www.omg.org/spec/DD/20100524/DI" typeLanguage="http://www.w3.org/2001/XMLSchema" expressionLanguage="http://www.w3.org/1999/XPath" targetNamespace="http://www.activiti.org/test">
  <signal id="alertSignal" name="alert" activiti:scope="global"></signal>
  <process id="myProcess" name="My process" isExecutable="true">
    <startEvent id="startevent1" name="Start"></startEvent>
    <eventBasedGateway id="eventgateway1" name="Event Gateway"></eventBasedGateway>
    <sequenceFlow id="flow1" sourceRef="startevent1" targetRef="eventgateway1"></sequenceFlow>
    <intermediateCatchEvent id="timerintermediatecatchevent1" name="TimerCatchEvent">
      <timerEventDefinition>
        <timeDuration>PT10M</timeDuration>
      </timerEventDefinition>
    </intermediateCatchEvent>
    <sequenceFlow id="flow2" sourceRef="eventgateway1" targetRef="timerintermediatecatchevent1"></sequenceFlow>
    <intermediateCatchEvent id="signalintermediatecatchevent1" name="警告信号">
      <signalEventDefinition signalRef="alertSignal"></signalEventDefinition>
    </intermediateCatchEvent>
    <sequenceFlow id="flow3" sourceRef="eventgateway1" targetRef="signalintermediatecatchevent1"></sequenceFlow>
    <userTask id="usertask1" name="处理警告"></userTask>
    <sequenceFlow id="flow4" sourceRef="signalintermediatecatchevent1" targetRef="usertask1"></sequenceFlow>
    <exclusiveGateway id="exclusivegateway1" name="Exclusive Gateway"></exclusiveGateway>
    <endEvent id="endevent1" name="End"></endEvent>
    <sequenceFlow id="flow5" sourceRef="exclusivegateway1" targetRef="endevent1"></sequenceFlow>
    <sequenceFlow id="flow6" sourceRef="usertask1" targetRef="exclusivegateway1"></sequenceFlow>
    <sequenceFlow id="flow7" sourceRef="timerintermediatecatchevent1" targetRef="endevent1"></sequenceFlow>
    <textAnnotation id="textannotation1">
      <text>警告事件</text>
    </textAnnotation>
    <textAnnotation id="textannotation2">
      <text>10分钟的定时器</text>
    </textAnnotation>
    <association id="association1" sourceRef="signalintermediatecatchevent1" targetRef="textannotation1"></association>
    <association id="association2" sourceRef="timerintermediatecatchevent1" targetRef="textannotation2"></association>
  </process>
```

- **Java 代码**  
```java

```

- **参考链接**  
http://www.mossle.com/docs/activiti/index.html#bpmnInclusiveGateway  
http://www.activiti.org/userguide/index.html#bpmnEventbasedGateway  
