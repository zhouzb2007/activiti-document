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

- **代码**

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



# 参考链接  
[Activiti进阶（九）——接收任务（ReceiveTask）][java_receive_task_url]




[java_receive_task_url]:http://blog.csdn.net/zjx86320/article/details/50385444
