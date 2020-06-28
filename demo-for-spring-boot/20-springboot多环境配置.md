# Spring Boot的多环境配置

语法：application-{profile}.properties/yaml

profile：代表某个配置环境的标识

实例：

- application-dev.yml：开发
- application-test.yml：测试
- application-prod.yml：生产

windows环境下启动：

java -jar xxx.jar --spring.profiles.active={profile}

