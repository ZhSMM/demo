# bootstrap配置文件

### bootstrap配置文件介绍

Spring Boot中存在两种上下文对象，一种是 bootstrap ，另一种是 application。

- bootstrap：是应用程序的父上下文，加载优先于 application ；
  - bootstrap 主要用于从额外的资源来加载配置信息
  - 还可以在本地外部配置文件中解密属性
- bootstrap 和 application 共用一个环境，它是任何Spring 应用程序的外部属性的来源。bootstrap里面的属性会优先加载，默认不能被本地相同配置覆盖。

### bootstrap配置文件特性

- bootstrap 由父ApplicationContext加载，比application优先加载；
- bootstrap 里面的属性不能被覆盖；

### bootstrap与application应用场景

- application 配置文件主要用于Spring Boot项目的自动化配置
- bootstrap 配置文件用于以下几个场景：
  - 使用Spring Cloud Config 配置中心时，需要在bootstrap配置文件中添加连接到配置中心的配置属性来加载外部配置中心的配置信息；
  - 一些固定的不能被覆盖的属性；
  - 一些加密/解密的场景；