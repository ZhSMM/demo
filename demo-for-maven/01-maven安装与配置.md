# maven安装与配置

> [Maven](http://c.biancheng.net/maven/) 是 Apache 开源组织奉献的一个开源项目。Maven 这个词可以翻译为“知识的积累”，也可以翻译为“专家”或“内行”。
>
> Maven 的本质是一个项目管理工具，将项目开发和管理过程抽象成一个项目对象模型（POM）。开发人员只需做一些简单的配置，就可以批量完成项目的构建、报告和文档的生成工作。

### 作用

- Maven 统一集中管理好所有的依赖包，不需要程序员再去寻找。
- 对应第三方组件用到的共同 jar，Maven 自动解决重复和冲突问题。
- Maven 作为一个开放的架构，提供了公共接口，方便同第三方插件集成。程序员可以将自己需要的插件，动态地集成到 Maven，从而扩展新的管理功能。
- Maven 可以统一每个项目的构建过程，实现不同项目的兼容性管理。

### 安装

1. Maven下载网址：http://maven.apache.org/download.cgi

2. 解压到指定目录

3. 文件目录：

   - bin：
     - 该目录包含了 mvn 运行的脚本，这些脚本用来配置 Java 命令，准备好 classpath 和相关的 Java 系统属性，然后执行 Java 命令。
     - 其中 mvn 是基于 UNIX 平台的 shell 脚本，mvn.bat 是基于 Windows 平台的 bat 脚本。在命令行输入任何一条 mvn 命令时，实际上就是在调用这些脚本。
     - mvnDebug：比 mvn 多了一条 MAVEN_DEBUG_OPTS 配置，其作用就是在运行 Maven 时开启 debug，以便调试 Maven 本身。
     - 此外，该目录还包含 m2.conf 文件，这是 classworlds 的配置文件，后面会介绍 classworlds。
   - boot：
     - 该目录只包含一个文件，以 maven 3.3.9 为例，该文件为 plexus-classworlds-2.5.2.jar。
     - plexus-classworlds 是一个类加载器框架，相对于默认的 java 类加载器，它提供了更丰富的语法以方便配置，Maven 使用该框架加载自己的类库。
   - conf：
     - settings.xml：直接修改该文件，就能在机器上全局地定制 Maven 的行为。
     - 一般情况下，我们更偏向于复制该文件至 `～/.m2/ 目录下（～表示用户目录）`，然后修改该文件，在用户范围定制 Maven 的行为。后面将会多次提到 settings.xml，并逐步分析其中的各个元素。
   - lib：
     - 该目录包含了所有 Maven 运行时需要的 Java 类库，Maven 本身是分模块开发的，因此用户能看到诸如 maven-core-3.0.jar、maven-model-3.0.jar 之类的文件。
     - 此外，这里还包含一些 Maven 用到的第三方依赖，如 common-cli-1.2.jar、commons-lang-2.6.jar 等。
   - LICENSE.txt：
     - 记录了 Maven 使用的软件许可证Apache License Version 2.0。
   - NOTICE.txt：
     - 记录了 Maven 包含的第三方软件。
   - README.txt：
     - 包含了 Maven 的简要介绍，包括安装需求及如何安装的简要指令等。

4. 配置maven：

   - MAVEN_HOME：安装目录
   - path：`%MAVEN_HOME%\bin`

5. 配置maven本地仓库：在maven解压文件中，conf目录中修改settings.xml文件；

   ```
     <!-- localRepository
      | The path to the local repository maven will use to store artifacts.
      |
      | Default: ${user.home}/.m2/repository
     <localRepository>/path/to/local/repo</localRepository>
     -->
   <localRepository>本地仓库的路径</localRepository>
   ```

6. 配置中央仓库的镜像，换成阿里的，在settings.xml文件中，在mirrors标签中添加子标签：

   参考阿里官方文档：https://developer.aliyun.com/mirror/maven

   ```xml
   <mirror>
       <id>nexus-aliyun</id>
       <mirrorOf>central</mirrorOf>
       <name>nexus-aliyun</name>
       <!-- 或使用https://maven.aliyun.com/repository/public -->
       <url>http://maven.aliyun.com/nexus/content/groups/public</url>
   </mirror>
   ```

7. JDK配置：

   > 在 profile 标签中增加一个 profile 标签，限定maven项目默认的jdk版本，配置如下：

   ```xml
   <profiles>
   	<!-- 在已有的profiles标签中添加profile标签-->
       <profile>
       	<id>my-jdk</id>
           <activation>
           	<activeByDefault>true</activeByDefault>
               <jdk>1.8</jdk>
           </activation>
           <properties>
           	<maven.compiler.source>1.8</maven.compiler.source>
               <maven.compiler.target>1.8</maven.compiler.target>
               <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
           </properties>
       </profile>
   </profiles>
   
   <!-- 让增加的profile生效 -->
   <activeProfiles>
   	<activeProfile>my-jdk</activeProfile>
   </activeProfiles>
   ```

8. 测试：

   - `mvn -v`
   - `mvn help:system`

### 参考网址

- [C语言中文网--Maven](http://c.biancheng.net/view/5001.html)