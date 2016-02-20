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

![java-receive-task-diagram](http://7xphqb.com1.z0.glb.clouddn.com/7b9b59c5ee24b83f81ac7193fa6a5b7b.png)


- **XML代码**  

```XML
<?xml version="1.0" encoding="UTF-8"?>
<definitions xmlns="http://www.omg.org/spec/BPMN/20100524/MODEL" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:activiti="http://activiti.org/bpmn" xmlns:bpmndi="http://www.omg.org/spec/BPMN/20100524/DI" xmlns:omgdc="http://www.omg.org/spec/DD/20100524/DC" xmlns:omgdi="http://www.omg.org/spec/DD/20100524/DI" typeLanguage="http://www.w3.org/2001/XMLSchema" expressionLanguage="http://www.w3.org/1999/XPath" targetNamespace="http://www.activiti.org/test">
  <process id="javaReceiveTaskProcess" name="接收任务测试流程" isExecutable="true">
    <startEvent id="startevent1" name="Start"></startEvent>
    <receiveTask id="receivetask1" name="汇总当日销售额"></receiveTask>
    <endEvent id="endevent1" name="End"></endEvent>
    <sequenceFlow id="flow2" sourceRef="receivetask1" targetRef="receivetask2"></sequenceFlow>
    <sequenceFlow id="flow3" sourceRef="startevent1" targetRef="receivetask1"></sequenceFlow>
    <receiveTask id="receivetask2" name="给老板发短信"></receiveTask>
    <sequenceFlow id="flow4" sourceRef="receivetask2" targetRef="endevent1"></sequenceFlow>
  </process>
  <bpmndi:BPMNDiagram id="BPMNDiagram_javaReceiveTaskProcess">
    <bpmndi:BPMNPlane bpmnElement="javaReceiveTaskProcess" id="BPMNPlane_javaReceiveTaskProcess">
      <bpmndi:BPMNShape bpmnElement="startevent1" id="BPMNShape_startevent1">
        <omgdc:Bounds height="35.0" width="35.0" x="69.0" y="250.0"></omgdc:Bounds>
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="receivetask1" id="BPMNShape_receivetask1">
        <omgdc:Bounds height="55.0" width="105.0" x="170.0" y="240.0"></omgdc:Bounds>
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="endevent1" id="BPMNShape_endevent1">
        <omgdc:Bounds height="35.0" width="35.0" x="520.0" y="250.0"></omgdc:Bounds>
      </bpmndi:BPMNShape>
      <bpmndi:BPMNShape bpmnElement="receivetask2" id="BPMNShape_receivetask2">
        <omgdc:Bounds height="55.0" width="105.0" x="350.0" y="240.0"></omgdc:Bounds>
      </bpmndi:BPMNShape>
      <bpmndi:BPMNEdge bpmnElement="flow2" id="BPMNEdge_flow2">
        <omgdi:waypoint x="275.0" y="267.0"></omgdi:waypoint>
        <omgdi:waypoint x="350.0" y="267.0"></omgdi:waypoint>
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="flow3" id="BPMNEdge_flow3">
        <omgdi:waypoint x="104.0" y="267.0"></omgdi:waypoint>
        <omgdi:waypoint x="170.0" y="267.0"></omgdi:waypoint>
      </bpmndi:BPMNEdge>
      <bpmndi:BPMNEdge bpmnElement="flow4" id="BPMNEdge_flow4">
        <omgdi:waypoint x="455.0" y="267.0"></omgdi:waypoint>
        <omgdi:waypoint x="520.0" y="267.0"></omgdi:waypoint>
      </bpmndi:BPMNEdge>
    </bpmndi:BPMNPlane>
  </bpmndi:BPMNDiagram>
</definitions>
```

>要在接收任务等待的流程实例继续执行， 可以调用runtimeService.signal(executionId)，传递接收任务上流程的id。 下面的代码演示了实际是如何工作的：

- **Java 代码**  

```java
package com.alfrescoblog.MyTest;

import java.io.InputStream;

import junit.framework.TestCase;

import org.activiti.engine.ProcessEngine;
import org.activiti.engine.ProcessEngineConfiguration;
import org.activiti.engine.ProcessEngines;
import org.activiti.engine.repository.Deployment;
import org.activiti.engine.runtime.Execution;
import org.activiti.engine.runtime.ProcessInstance;

public class ReceiveTaskTest extends TestCase {

	ProcessEngineConfiguration processEngineConfiguration = ProcessEngineConfiguration
			.createProcessEngineConfigurationFromResource("activiti.cfg.xml","processEngineConfiguration");

	// 流程引擎对象
	ProcessEngine processEngine = ProcessEngines
			.getProcessEngine(processEngineConfiguration.getProcessEngineName());

	public void testReceiveTask() {
		InputStream inputStream = this.getClass().getClassLoader().getResourceAsStream(
				"java-receive-task.bpmn");

		// 1 部署流程定义
		Deployment deployment = processEngine.getRepositoryService()
				.createDeployment()
				.addInputStream("java-receive-task.bpmn", inputStream).deploy();
		System.out.println("部署ID:" + deployment.getId());

		// 2 根据流程id启动流程
		ProcessInstance processInstance = processEngine.getRuntimeService()
				.startProcessInstanceByKey("javaReceiveTaskProcess");
		System.out.println("流程实例ID" + processInstance.getId());
		System.out.println("流程定义ID" + processInstance.getProcessDefinitionId());

		// 3 使用实例id与当前活动的名称，查询执行对象
		String processInstanceId = processInstance.getId();
		Execution execution1 = processEngine.getRuntimeService()
				.createExecutionQuery().processInstanceId(processInstanceId)
				.activityId("receivetask1").singleResult();

		// 4 使用流程变量设置当前销售额
		processEngine.getRuntimeService().setVariable(execution1.getId(),
				"当日销售额", 1000000);

		// 5 给receivetask1一个信号，向后推动流程
		processEngine.getRuntimeService().signal(execution1.getId());

		// 6 使用实例id与当前活动的名称，查询执行对象
		Execution execution2 = processEngine.getRuntimeService()
				.createExecutionQuery().processInstanceId(processInstanceId)
				.activityId("receivetask2").singleResult();

		// 7 获取流程变量，給老板发送短信
		Integer value = (Integer) processEngine.getRuntimeService()
				.getVariable(execution2.getId(), "当日销售额");
		System.out.println("老板，我们今天销售很给力。 毛利润: " + value);

		// 8.向后执行一步
		processEngine.getRuntimeService().signal(execution2.getId());

		// 9.判断流程是否结束
		ProcessInstance nowPi = processEngine.getRuntimeService()
				.createProcessInstanceQuery()
				.processInstanceId(processInstance.getId()).singleResult();
		if (nowPi == null) {
			System.out.println("流程结束");
		}
	}
}
```


- **参考连接**  

[Activiti进阶（九）——接收任务（ReceiveTask）][java_receive_task_url]

## 业务规则任务  

- **描述**  

英文描述  

A Business Rule task is used to synchronously execute one or more rules. Activiti uses Drools Expert, the Drools rule engine to execute business rules. Currently, the .drl files containing the business rules have to be deployed together with the process definition that defines a business rule task to execute those rules. This means that all .drl files that are used in a process have to be packaged in the process BAR file like for example the task forms. For more information about creating business rules for Drools Expert please refer to the Drools documentation at JBoss Drools

If you want to plug in your implementation of the rule task, e.g. because you want to use Drools differently or you want to use a completely different rule engine, then you can use the class or expression attribute on the BusinessRuleTask and it will behave exactly like a ServiceTask


中文描述  

业务规则任务用来同步执行一个或多个规则。activiti使用drools规则引擎执行业务规则。 目前，包含业务规则的.drl文件必须和流程定义一起发布，流程定义里包含了执行这些规则的业务规则任务。 意味着流程使用的所有.drl文件都必须打包在流程BAR文件里，比如任务表单。 更多使用Drools Expert创建业务规则的信息，请参考JBoss Drools的文档。
如果想要使用你自己实现的规则任务，比如，因为你想用不同方式使用drools，或你想使用完全不同的规则引擎， 你可以使用BusinessRuleTask上的class或表达式属性，它用起来就和 ServiceTask一样。


- **图形标记**  

英文描述

A Business Rule task is visualized the with a table icon.


中文描述

业务规则任务用表格图标标识

![业务规则任务](http://7xphqb.com1.z0.glb.clouddn.com/cb15feb46a2019c03c572a0b53c66ef2.png)

- **实例**  




- **XML代码**  



- **Java 代码**  


- **参考链接**  



## 事物子流程  

- **描述**  

英文描述
中文描述

- **图形标记**  

- **实例**  

- **XML代码**

- **Java 代码**  

## 包含网管

- **描述**  

英文描述
中文描述

- **图形标记**  

- **实例**  

- **XML代码**

- **Java 代码**  

## 基于事件的网管

- **描述**  

英文描述
中文描述

- **图形标记**  

- **实例**  

- **XML代码**

- **Java 代码**  


# 容器
# 网关

# 边界事件

## 消息边界事件

- **描述**  

英文描述
中文描述

- **图形标记**  

- **实例**  

- **XML代码**

- **Java 代码**  

## 取消边界事件

- **描述**  

英文描述
中文描述

- **图形标记**  

- **实例**  

- **XML代码**

- **Java 代码**  

## 补偿边界事件

- **描述**  

英文描述
中文描述

- **图形标记**  

- **实例**  

- **XML代码**

- **Java 代码**  

## 消息捕获事件

- **描述**  

英文描述
中文描述

- **图形标记**  

- **实例**  

- **XML代码**

- **Java 代码**  

## 补偿抛出事件  

- **描述**  

英文描述
中文描述

- **图形标记**  

- **实例**  

- **XML代码**

- **Java 代码**  

## 空抛出事件

- **描述**  

英文描述
中文描述

- **图形标记**  

- **实例**  

- **XML代码**

- **Java 代码**  


# 中断事件
# 注释
# 连接  



# 参考链接  





[java_receive_task_url]:http://blog.csdn.net/zjx86320/article/details/50385444
