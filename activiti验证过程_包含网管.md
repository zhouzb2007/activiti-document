- **描述**

英文描述  

The Inclusive Gateway can be seen as a combination of an exclusive and a parallel gateway. Like an exclusive gateway you can define conditions on outgoing sequence flows and the inclusive gateway will evaluate them. But the main difference is that the inclusive gateway can take more than one sequence flow, like the parallel gateway.

The functionality of the inclusive gateway is based on the incoming and outgoing sequence flow:

- fork: all outgoing sequence flow conditions are evaluated and for the sequence flow conditions that evaluate to true the flows are followed in parallel, creating one concurrent execution for each sequence flow.
- join: all concurrent executions arriving at the inclusive gateway wait in the gateway until an execution has arrived for each of the incoming sequence flows that have a process token. This is an important difference with the parallel gateway. So in other words, the inclusive gateway will only wait for the incoming sequence flows that will be executed. After the join, the process continues past the joining inclusive gateway.
>Note that an inclusive gateway can have both fork and join behavior, if there are multiple incoming and outgoing sequence flow for the same inclusive gateway. In that case, the gateway will first join all incoming sequence flows that have a process token, before splitting into multiple concurrent paths of executions for the outgoing sequence flows that have a condition that evaluates to true.


中文描述

包含网关可以看做是排他网关和并行网关的结合体。 和排他网关一样，你可以在外出顺序流上定义条件，包含网关会解析它们。 但是主要的区别是包含网关可以选择多于一条顺序流，这和并行网关一样。
包含网关的功能是基于进入和外出顺序流的：
- 分支： 所有外出顺序流的条件都会被解析，结果为true的顺序流会以并行方式继续执行， 会为每个顺序流创建一个分支。
- 汇聚： 所有并行分支到达包含网关，会进入等待章台， 直到每个包含流程token的进入顺序流的分支都到达。 这是与并行网关的最大不同。换句话说，包含网关只会等待被选中执行了的进入顺序流。 在汇聚之后，流程会穿过包含网关继续执行。
>注意，如果同一个包含节点拥有多个进入和外出顺序流， 它就会同时含有分支和汇聚功能。 这时，网关会先汇聚所有拥有流程token的进入顺序流， 再根据条件判断结果为true的外出顺序流，为它们生成多条并行分支。

- **图形标记**

![包含网管图标](http://7xphqb.com1.z0.glb.clouddn.com/0536f91de60008a30c1ad2a97831daaf.png)


- **实例**  
![包含网管流程图](http://7xphqb.com1.z0.glb.clouddn.com/63cd8a040f3c545af8d7b14193d814fa.png)

- **XML代码**
xml核心代码

```xml
<?xml version="1.0" encoding="UTF-8"?>
<definitions xmlns="http://www.omg.org/spec/BPMN/20100524/MODEL" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:activiti="http://activiti.org/bpmn" xmlns:bpmndi="http://www.omg.org/spec/BPMN/20100524/DI" xmlns:omgdc="http://www.omg.org/spec/DD/20100524/DC" xmlns:omgdi="http://www.omg.org/spec/DD/20100524/DI" typeLanguage="http://www.w3.org/2001/XMLSchema" expressionLanguage="http://www.w3.org/1999/XPath" targetNamespace="http://www.activiti.org/test">
  <process id="myIncluesiveGatewayProcess" name="排他网管测试流程" isExecutable="true">
    <startEvent id="startevent1" name="Start"></startEvent>
    <userTask id="usertask1" name="天庭选拔大赛"></userTask>
    <sequenceFlow id="flow1" sourceRef="startevent1" targetRef="usertask1"></sequenceFlow>
    <inclusiveGateway id="inclusivegateway1" name="Inclusive Gateway"></inclusiveGateway>
    <sequenceFlow id="flow2" sourceRef="usertask1" targetRef="inclusivegateway1"></sequenceFlow>
    <userTask id="usertask2" name="上天当神仙"></userTask>
    <sequenceFlow id="flow3" name="生肖属性为猴" sourceRef="inclusivegateway1" targetRef="usertask2">
      <conditionExpression xsi:type="tFormalExpression"><![CDATA[${property == "monkey"}]]></conditionExpression>
    </sequenceFlow>
    <userTask id="usertask3" name="并行任务-妖怪吃的唐僧肉"></userTask>
    <sequenceFlow id="flow4" name="生肖属性不为猴" sourceRef="inclusivegateway1" targetRef="usertask3">
      <conditionExpression xsi:type="tFormalExpression"><![CDATA[${property != "monkey"}]]></conditionExpression>
    </sequenceFlow>
    <userTask id="usertask4" name="并行任务-人要经历81难"></userTask>
    <sequenceFlow id="flow5" name="生肖属性不为猴" sourceRef="inclusivegateway1" targetRef="usertask4">
      <conditionExpression xsi:type="tFormalExpression"><![CDATA[${property != "monkey"}]]></conditionExpression>
    </sequenceFlow>
    <inclusiveGateway id="inclusivegateway2" name="Inclusive Gateway"></inclusiveGateway>
    <sequenceFlow id="flow6" sourceRef="usertask3" targetRef="inclusivegateway2"></sequenceFlow>
    <sequenceFlow id="flow7" sourceRef="usertask4" targetRef="inclusivegateway2"></sequenceFlow>
    <endEvent id="endevent1" name="End"></endEvent>
    <sequenceFlow id="flow8" sourceRef="usertask2" targetRef="endevent1"></sequenceFlow>
    <sequenceFlow id="flow9" sourceRef="inclusivegateway2" targetRef="usertask5"></sequenceFlow>
    <userTask id="usertask5" name="封神"></userTask>
    <sequenceFlow id="flow10" sourceRef="usertask5" targetRef="endevent1"></sequenceFlow>
  </process>
```


- **Java 代码**

Java 核心代码

```java
ProcessEngineConfiguration processEngineConfiguration = ProcessEngineConfiguration
			.createProcessEngineConfigurationFromResource("activiti.cfg.xml",
					"processEngineConfiguration");
	// 流程引擎对象
	ProcessEngine processEngine = ProcessEngines
			.getProcessEngine(processEngineConfiguration.getProcessEngineName());

	private void executeProcess(String keyStr){
		RuntimeService runtimeService = processEngine.getRuntimeService();

		ProcessInstance pi = runtimeService
				.startProcessInstanceByKey("myIncluesiveGatewayProcess");

		Map<String, Object> vars = new HashMap<String, Object>();
		vars.put("property", keyStr);
		TaskService taskService = processEngine.getTaskService();
		/**
		 * 当前任务
		 */
		List<Task> tasks = taskService.createTaskQuery()
				.processInstanceId(pi.getId()).list();
		System.out.println("如果是property是monkey则有一个任务，否则同时又两个任务: "+tasks.size());

		for (Task task : tasks) {
			System.out.println(task.getId() + " , " + task.getName());
			taskService.complete(task.getId(),vars);
		}

		/**
		 * 下一步任务
		 */
		tasks = taskService.createTaskQuery().processInstanceId(pi.getId())
				.list();
		System.out.println("如果是property是monkey则有一个任务，否则同时又两个任务: "+tasks.size());
		for (Task task : tasks) {
			System.out.println(task.getId() + " , " + task.getName());
			taskService.complete(task.getId());
		}


		if (keyStr != "monkey"){
			tasks = taskService.createTaskQuery()
					.processInstanceId(pi.getId()).list();
			System.out.println("如果是property是monkey则有一个任务，否则同时又两个任务: "+tasks.size());

			for (Task task : tasks) {
				System.out.println(task.getId() + " , " + task.getName());
				taskService.complete(task.getId());
			}
		}

		ProcessInstance nowPi = processEngine.getRuntimeService()
				.createProcessInstanceQuery()
				.processInstanceId(pi.getId()).singleResult();
		if (nowPi == null) {
			System.out.println("流程结束");
		}
	}

	public void testIncluesiveGateway(){

		try{
			InputStream bpmnInputStream = this.getClass().getClassLoader()
					.getResourceAsStream("IncluesiveGatewayProcess.bpmn");

			Deployment deployment = processEngine
					.getRepositoryService()
					.createDeployment()
					.addInputStream("IncluesiveGatewayProcess.bpmn", bpmnInputStream)
					.deploy();
			System.out.println("部署ID:" + deployment.getId());

			System.out.println("节点有一个任务");
			executeProcess("monkey");
			System.out.println("节点有两个任务");
			executeProcess("person");
		}catch(Exception e){
			e.printStackTrace();
		}
	}
```  

验证输出结果  
```shell
部署ID:170001
节点有一个任务
如果是property是monkey则有一个任务，否则同时又两个任务: 1
170008 , 天庭选拔大赛
如果是property是monkey则有一个任务，否则同时又两个任务: 1
170012 , 上天当神仙
流程结束
节点有两个任务
如果是property是monkey则有一个任务，否则同时又两个任务: 1
170017 , 天庭选拔大赛
如果是property是monkey则有一个任务，否则同时又两个任务: 2
170023 , 并行任务-妖怪吃的唐僧肉
170025 , 并行任务-人要经历81难
如果是property是monkey则有一个任务，否则同时又两个任务: 1
170029 , 封神
流程结束

```

- **参考链接**  
http://www.activiti.org/userguide/index.html#bpmnInclusiveGateway
http://www.mossle.com/docs/activiti/index.html#bpmnInclusiveGateway
http://www.tuicool.com/articles/ZZzaum
