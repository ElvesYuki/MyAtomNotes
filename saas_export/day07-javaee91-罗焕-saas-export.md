## 今日内容：分布式框架Duboo

**相关概念**：

**软件架构发展**：

​	单一应用架构 --> 垂直应用架构 --> 分布式架构 -- 流动计算架构

​	**流动计算架构 = 分布式架构 + 监控 + 调度**

**SOA**：面向服务的架构

​	如何通过web调用服务：**RPC**（服务之间的远程调用，remote procedure call）

**dubbo**：是一个apache开源的rpc框架

​		（是一款高性能的 **Java RPC框架**）

**服务的消费者**：web工程，关注了controller，调用服务提供者

**服务的提供者**：service工程，暴露了所有的业务服务，被消费者调用

**注册中心**        ：将所有提供者服务信息进行收集管理

**监控中心**        ：监控消费者和提供者是否正常运行

**企业申请案例**：

**步骤**：

​	在父工程下建立三个子工程：service接口工程，service实现类工程，web工程

​	**依赖关系**：

​	service实现类工程**依赖**service接口工程，**依赖**dao工程

​	web工程**依赖**service接口工程

​	service接口工程**依赖**实体类工程

​	dao工程也**依赖**实体类工程（之前已经配置好了）

**service接口工程**里只有 **CompanyService接口**；

**service实现类工程**里有**CompanyServiceImpl实现类**，

​	pom.xml文件手动添加dubbo依赖

​	有配置文件：**applicationContext-dubbo.xml**--dubbo的配置文件，

​				applicationContext-service.xml**--spring的配置文件，

​				**web.xml**中配置监听器**加载**所有配置文件

​	(加载三个配置文件，上面两个还有**applicationContext-dao.xml**--dao的配置文件）

​	可以在test中配置main方法进行测试提供者。

**web工程**里有	**ApplyController类**

​	pom.xml文件手动添加dubbo依赖

​	springmvc.xml**配置文件（配置了springmvc的包扫描和dubbo配置）

​	**web.xml**配置文件（加载springmvc.xml配置文件还有页面配置）

​	前端页面	

出现问题：

​	第一次写消费者启动失败。

​	日志：

~~~java
org.apache.catalina.core.ApplicationContext.log No Spring WebApplicationInitializer types detected on classpath org.apache.catalina.core.StandardContext.filterStart Exception starting filter [CharacterEncodingFilter]
 java.lang.NoSuchMethodError: org.springframework.util.ObjectUtils.unwrapOptional(Ljava/lang/Object;)Ljava/lang/Object;
~~~

查找好像是**依赖冲突**；

后找到原因，可能是**未在父工程下建立子工程**，导致子工程中pom.xml文件中没有了父工程依赖，对比手动添加后不能依旧不能启动，遂重写企业申请模块。

在父工程下创建子工程，自动添加了父工程依赖。

这次提供者和消费者两个模块都不能启动了😭

检查代码发现：

​	![1561211498678](F:\黑马程序员\MyAtomNotes\images\1561211498678.png)



​		实现类上忘记添加@Service注解（！！！为dubbo的注解而不是spring的）



![1561211579854](F:\黑马程序员\MyAtomNotes\images\1561211579854.png)		

​		web工程忘记添加@Controller注解（！！！此为spring的注解）



然后重新启动两个模块，OK！！！

不过打开　ｄｕｂｂｏ－ａｄｍｉｎ中，显示无消费者，

![1561211882595](F:\黑马程序员\MyAtomNotes\images\1561211882595.png)

以为是未启动成果，排查好久，后来发现是应该**消费者**未调用**提供者**；

页面正常打开，访问注册后，ｄｕｂｂｏ状态变为正常！！！

![1561212022216](F:\黑马程序员\MyAtomNotes\images\1561212022216.png)

