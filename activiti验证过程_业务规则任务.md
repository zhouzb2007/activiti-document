## 业务规则任务  
>业务规则引擎，业务与程序分离，流程引擎，使流程与程序分离


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


- **参数说明**

|属性名称|属性说明|示例
| :- | :- | :- |
|`activiti:rules` |在规则文件.drl中定义的规则名称，多个规则使用逗号分隔。要执行规则文件中的全部规则，该属性设置为空即可 | `<businessRuleTask id="businessruletask1" activiti:rules="rule1,rule2" />` |
|`activiti:ruleVariableslnput` | 业务规则执行需要的数据源，使用${ fooVar｝方式定义，多个规则用逗号分隔 | `<businessRuleTask id="businessruletask I" activiti:ruleVariableslnput="leave"` |
|`activiti :execlude` |用来设置是否排除某些规则( ru l e ），如果值为false ，不排除按照activiti:rules 规则执行；如果设置为true ， 则忽略activiti : rules 指定的规则。如果设置为fa l se 的同时activiti:rules值为空．则不执行任何规则 | `<businessRuleTask id="businessruletask I" activiti:rules="rule2" activiti: execlude ="true" />`|
|`activiti: res ultVariableName` | 规则执行结果变量，变量的值为rule Variableslnput 定义的变量集合（ArrayList）| `<businessRuleTask id=”businessruletask I" activiti:rules=”rule2”activiti: execlude =”true" /> 此例中忽略规则rule2 ，仅执行规则rule1`|


> 要验证该业务规则任务，需要在流程引擎中继承drools规则引擎

- **实例**  

![业务规则验证流程](http://7xphqb.com1.z0.glb.clouddn.com/5667734ba8cba1e7e00c0a77fb8956a2.png)



- **XML代码**  
xml 核心代码
```xml
<?xml version="1.0" encoding="UTF-8"?>
<definitions xmlns="http://www.omg.org/spec/BPMN/20100524/MODEL" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:activiti="http://activiti.org/bpmn" xmlns:bpmndi="http://www.omg.org/spec/BPMN/20100524/DI" xmlns:omgdc="http://www.omg.org/spec/DD/20100524/DC" xmlns:omgdi="http://www.omg.org/spec/DD/20100524/DI" typeLanguage="http://www.w3.org/2001/XMLSchema" expressionLanguage="http://www.w3.org/1999/XPath" targetNamespace="http://www.activiti.org/test">
  <process id="myBusinessRuleProcess" name="My process" isExecutable="true">
    <startEvent id="startevent1" name="开始请假"></startEvent>
    <businessRuleTask id="businessruletask1" name="天数判断" activiti:ruleVariablesInput="${leave}" activiti:rules="leave1,leave2" activiti:resultVariable="reason"></businessRuleTask>
    <userTask id="team_approve" name="部门经理审批"></userTask>
    <sequenceFlow id="flow1" sourceRef="startevent1" targetRef="team_approve"></sequenceFlow>
    <exclusiveGateway id="exclusivegateway1" name="Exclusive Gateway"></exclusiveGateway>
    <sequenceFlow id="flow2" sourceRef="businessruletask1" targetRef="servicetask1"></sequenceFlow>
    <userTask id="ceo_approve" name="总经理审批"></userTask>
    <sequenceFlow id="flow3" sourceRef="exclusivegateway1" targetRef="ceo_approve">
      <conditionExpression xsi:type="tFormalExpression"><![CDATA[${reason[0].total >= 10}]]></conditionExpression>
    </sequenceFlow>
    <userTask id="hr_approve" name="hr审批"></userTask>
    <sequenceFlow id="flow4" sourceRef="exclusivegateway1" targetRef="hr_approve">
      <conditionExpression xsi:type="tFormalExpression"><![CDATA[${reason[0].total < 10}]]></conditionExpression>
    </sequenceFlow>
    <sequenceFlow id="flow5" sourceRef="team_approve" targetRef="businessruletask1"></sequenceFlow>
    <endEvent id="endevent1" name="请假结束"></endEvent>
    <sequenceFlow id="flow6" sourceRef="ceo_approve" targetRef="endevent1"></sequenceFlow>
    <sequenceFlow id="flow7" sourceRef="hr_approve" targetRef="endevent1"></sequenceFlow>
    <serviceTask id="servicetask1" name="打印计算后的变量" activiti:class="com.kopbit.activiti.service.DroolsService"></serviceTask>
    <sequenceFlow id="flow8" sourceRef="servicetask1" targetRef="exclusivegateway1"></sequenceFlow>
  </process>
```


- **Java 代码**  

java核心代码

```java
try {
			InputStream bpmnInputStream = this.getClass().getClassLoader()
					.getResourceAsStream("BusinessRuleProcess.bpmn");
			InputStream droolsInputStream = this.getClass().getClassLoader()
					.getResourceAsStream("BusinessRule.drl");
			Deployment deployment = processEngine
					.getRepositoryService()
					.createDeployment()
					.addInputStream("BusinessRuleProcess.bpmn", bpmnInputStream)
					.addInputStream("BusinessRule.drl", droolsInputStream)
					.deploy();
			System.out.println("部署ID:" + deployment.getId());
			RuntimeService runtimeService = processEngine.getRuntimeService();
			ProcessInstance pi = runtimeService
					.startProcessInstanceByKey("myBusinessRuleProcess");
			TaskService taskService = processEngine.getTaskService();
			Map<String, Object> vars = new HashMap<String, Object>();
			vars.put("leave", new Leave("盘古", 3));
			/**
			 * 当前任务
			 */
			List<Task> tasks = taskService.createTaskQuery()
					.processInstanceId(pi.getId()).list();
			for (Task task : tasks) {
				System.out.println(task.getId() + " , " + task.getName());
				taskService.complete(task.getId(), vars);
			}
			/**
			 * 下一步任务
			 */
			tasks = taskService.createTaskQuery().processInstanceId(pi.getId())
					.list();
			for (Task task : tasks) {
				System.out.println(task.getId() + " , " + task.getName());
			}
		} catch (Exception e) {
			e.printStackTrace();
			// System.out.println(e.getMessage());
		}
```  

DroolsService.java  

```java
public void execute(DelegateExecution execution) throws Exception {
		// TODO Auto-generated method stub
		//打印执行过程中的变量
		ProcessEngineConfiguration processEngineConfiguration = ProcessEngineConfiguration
				.createProcessEngineConfigurationFromResource("activiti.cfg.xml",
						"processEngineConfiguration");

		ProcessEngine processEngine = ProcessEngines
				.getProcessEngine(processEngineConfiguration.getProcessEngineName());


		RuntimeService runtimeService = processEngine.getRuntimeService();

		System.out.println("+++++++规则引擎计算后的变量+++++++++++");
		System.out.println(execution.getVariable("reason"));
		System.out.println(execution.getVariable("leave"));
		System.out.println("+++++++规则引擎计算后的变量+++++++++++");
	}
```

其他脚本  
BusinessRule.drl  

```shell
package com.kopbit.activiti;
import com.kopbit.activiti.model.Leave;


rule "leave1"
	when
		$u12 : Leave(day < 3 );
	then
		$u12.setTotal($u12.getDay() + 2 );
end

rule "leave2"
	when
		$u12 : Leave(day >=3 );
	then
		$u12.setTotal($u12.getDay() + 5 );
end
```




- **参考链接**  
http://blog.csdn.net/mn960mn/article/details/47803297  
http://www.activiti.org/userguide/index.html  
http://www.mossle.com/docs/activiti/index.html  
http://www.drools.org/
