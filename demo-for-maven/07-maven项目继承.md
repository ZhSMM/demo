# Maven项目继承

pom.xml的继承关系：

- 父项目的packaging元素必须是pom

- 子项目的使用 parent 元素声明父模块：

  - group、artifactId、version：指定父项目的坐标
  - relativePath：表示父模块POM的相对路径，在项目构建时，maven会首先根据relativePath检查父POM

  ```xml
  <parent>
  	<groupId>com.example</groupId>
      <artifactId>parent</artifactId>
      <version>1.0-SNAPSHOT</version>
      <relativePath>../parent/pom.xml</relativePath>
  </parent>
  ```

可继承的POM元素：

- groupId：项目组ID
- version：项目版本，所以子项目不需要写版本号
- distributionManagement：项目的部署配置
- properties：自定义的Maven属性
- dependencies：项目的依赖配置
- dependencyManagement：项目的依赖管理配置
- build：包括项目的源码目录配置、输出目录配置、插件配置、插件管理配置等

依赖管理：

- dependencyManagement元素说明：让子模块继承到父模块的依赖配置，又能保证子模块依赖使用的灵活性。在dependencyManagement元素下的依赖声明不会引入实际的依赖，不过它能够约束dependencies下的依赖使用。

插件管理：

- Maven提供了dependencyManagement元素帮助管理依赖，类似地，Maven也提供了pluginManagement元素帮助管理插件。同dependencyManagement一样，在pluginManagement元素中配置的依赖不会造成实际的插件调用行为，当POM中配置了真正的plugin元素，并且其groupId和artifactId与pluginManagement中配置的插件匹配时，pluginManagement的配置才会影响实际的插件行为。