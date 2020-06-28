# maven的生命周期管理 

### maven生命周期管理

maven项目中导入的依赖可以做生命周期的管理：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.3.1.RELEASE</version>
    <type>pom</type>
    <!-- 生命周期 -->
    <scope>import</scope>
</dependency>
```

maven的生命周期 scope 有如下几种：

- compile：默认，指该依赖用于maven项目的所有阶段（测试、编译、运行、打包），且会随着项目一起被发布（被打包）
- provided：该依赖只需要在编译过程中提供，不会被打包
- runtime：只在运行时使用，适用运行和测试阶段，会被一起发布
- test：该依赖只用于测试，用于编译和运行测试，不会被打包
- system：类似provided，但maven不会在Repository中查找，而是在本地磁盘目录中查找，参与编译、测试、打包、运行
- import：用于导入子模块中使用dependencyManagement管理的依赖，注意，scope=import只能用在dependencyManagement里面，并且type=pom的dependency。

### import的出现缘由

在maven多模块项目中，为了保持模块间依赖的统一，常规做法是在parent model中，使用dependencyManagement预定义所有模块需要用到的dependency(依赖)。然后子model中根据实际需要引入parent中预定义的依赖。

优点：

- 依赖统一管理(parent中定义，需要变动dependency版本，只要修改一处即可)；
- 代码简洁(子model只需要指定groupId、artifactId即可)
- dependencyManagement只会影响现有依赖的配置，但不会引入依赖。即子model不会继承parent中dependencyManagement所有预定义的depandency，只引入需要的依赖即可，简单说就是“按需引入依赖”或者“按需继承”。因此，在parent中严禁直接使用depandencys预定义依赖，坏处是子model会自动继承depandencys中所有预定义依赖。

问题：

- 单继承：maven的继承跟java一样，单继承，也就是说子model中只能出现一个parent标签；parent模块中，dependencyManagement中预定义太多的依赖，造成pom文件过长，无法清晰管理。

解决：import scope依赖

使用：

1. maven2.9以上版本
2. 将dependency分类，每一类建立单独的pom文件
3. 在需要使用到这些依赖的子model中，使用dependencyManagement管理依赖，并import scope依赖
4. 注意：scope=import只能用于dependencyManagement中，且仅用于type=pom的dependency

优点：

- 便于依赖管理，分类
- 解决单继承问题，通过import pom文件达到依赖的目的（典型的非继承模式），从而不用从父类中引用依赖
- 父模块的pom会很干净，便于维护

原文链接：[cnblogs.com/huahua035/p/7680607.html](cnblogs.com/huahua035/p/7680607.html)