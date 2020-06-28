# Spring Boot整合Thymeleaf

### thymeleaf

thymeleaf的主要目标是将优雅的自然模板带到开发工作流程中，并在HTML浏览器中正确显示，并且可以作为静态原型，让开发团队更容易地协作。Thymeleaf能够处理HTML、XML、JavaScript、CSS甚至纯文本。

### 基本使用

#### 添加依赖pom.xml

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
```

注意：template属于安全目录，不允许外部基于url直接访问，跟static不一样。

#### 添加Controller

```java
@Controller
public class ThymeleafController {
    @GetMapping("/index")
    public String showPage(Model model) {
        model.addAttribute("msg","Thymeleaf测试");
        return "index";
    }
}
```

#### 在template中添加页面

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title th:text="${msg}"></title>
</head>
<body>
<h1 th:text="'First Thymeleaf Test'"></h1>
</body>
</html>
```

### Thymeleaf语法

在html标签中引入命名空间：`xmlns:th="http://www.thymeleaf.org"`

标准表达式：

- `${...}` : 变量表达式。
- `*{...}` : 选择表达式。
- `#{...}` : 消息 (i18n) 表达式。
- `@{...}` : 链接 (URL) 表达式。
- `~{...}` : 片段表达式。

表达式预处理：

关于表达式的最后一件事是知道表达式预处理，在`__`之间指定，如下所示:

```jsp
#{selection.__${sel.code}__}
```

上面代码中，第一个被执行的变量表达式是:`${sel.code}`，并且将使用它的结果作为表达式的一部分(假设`${sel.code}`的结果为:`ALL`)，在此处执行国际化的情况下(这将查找与关键`selection.ALL`消息)。

1. 字符串变量输出：

   - th:text  在页面中输出值

   - th:utext  输出转义后的文本

     ```html
     <div th:text="'<h1>世界</h1>'"></div>
     <div th:utext="'<h1>世界</h1>'"></div>
     
     注意事项：text和utext中存放的是字符串，所以需要用单引号 '' 将字符串括起来
     输出：
         <h1>世界</h1>
         世界
     ```

   - th:value  将一个值放入 input 标签的value中，如`<input type="text" th:value="name">`

2. 字符串操作

   thymeleaf提供了一些内置对象，内置对象可以直接在模板中使用。这些对象是以 # 引用的。

   使用内置对象的语法

   - 引用内置对象需要使用 # 

   - 大部分内置对象名称以 s 结尾，如strings、numbers、dates

     ```jsp
     ${#strings.isEmpty(key)}：判断字符串是否为空，如果为空返回true，否则返回false
     ${#strings.contains(msg,'T')}：判断字符串是否包含指定的字串，如果包含返回true，否则返回false
     ${#strings.startWith(msg,'a')}：判断当前字符串是否以字串开头，是返回true，否返回false
     ```

3. 日期格式化

   - ${#dates.format(key)}：格式化日期，默认以浏览器默认语言为格式化标准
   - ${#dates.format(key,'yyyy/MM/dd hh:mm:ss')}：自定义格式化
   - ${#dates.year(key)}：取出年
   - ${#dates.month(key)}：取出月
   - ${#dates.year(day)}：取出日

4. 条件判断

   - `th:if`：条件判断
   - `th:switch/th:case`：类似Java的switch语句，不一样的是，如果有多个匹配只显示第一个，`th:case="*"`表示Java中的default，即没有case的值为true时则显示`th:case="*"`的内容

5. 循环遍历：th:each，循环迭代，th:each的状态变量

   - index：当前迭代器的索引，从0开始
   - count：当前迭代器的计数，从1开始
   - size：被迭代对象的长度
   - odd/even：布尔值，判断循环是否是偶数/奇数 从0开始
   - first：布尔值，当前循环的是否是第一条，是返回true
   - last：布尔值，当前循环的是否是最后一条，是返回true

   th:each="value,item : ${list}" ：value指代每条变量，而item指的是每条变量的属性。

6. 操作域对象

   Controller自动注入：

   ```java
   @Autowired
   private HttpServletRequest request;
   ```

   1. HttpServletRequest

      Controller设值：`request.setAttribute("req","HttpServletRequest)`

      页面中取值：

      - `th:text="${#httpServletRequest.getAttribute('req')}"`
      - `th:text="${#request.getAttribute('req')}"`

   2. HttpSession

      Controller设值：`request.getSession().setAttribute("session","HttpSession)`

      页面中取值：

      - `th:text="${#httpServletRequest.getSession().getAttribute('session')}"`
      - `th:text="${#request.getSession().getAttribute('session')}"`
      - `th:text="${#session.getAttribute('session')}"`
      - `th:text="${session.ses}"`

   3. ServletContext

      Controller设值：`request.getSession().getServletContext.setAttribute("ser","HttpServletContext)`

      页面中取值：

      - `th:text="${#httpServletRequest.getSession().getServletContext.getAttribute('ser')}"`
      - `th:text="${#request.getSession().getServletContext.getAttribute('ser')}"`
      - `th:text="${#servletContext.getAttribute('app')}"`
      - `th:text="${application.app}"`

7. url表达式：`@{}`

   1. 绝对路径：`<a th:href="@{http://www.baidu.com}">绝对路径</a>`
   2. 相对路径：
      - 相对于当前项目的根：`<a th:href="@{/index}">相对路径</a>`
      - 相对于服务器根目录：`<a th:href="@{~/project1/index}">相对路径</a>`

8. 在url中传递参数：

   - 在普通格式的url中传递参数：
     - `<a th:href="@{~/test(id=1,name='zhangsan')}">普通带参1</a>`
     - `<a th:href="@{~/test?id=2&name=zhangsan}">普通带参2</a>`
     - `<a th:href="@{'/test?id='+${id}+'&name='+${msg}}">普通带参3</a>`
     - `<a th:href="@{~/test(id=${id},name=${session.ses})}">普通带参4</a>`
   - 在restful格式的url中传递参数：
     - `<a th:href="@{~/hello/{id}/{name}(id=2,name='admin')}">Restful带参1</a>`：`http://localhost:8080/hello/2/admin`
     - `<a th:href="@{~/hello/{id}/{name}(id=${id},name=${msg})}">Restful带参2</a>`：`http://localhost:8080/hello/10/admin`
     - `<a th:href="@{~/hello/{id}(id=${id},name=${msg})}">Restful带参3</a>`：`http://localhost:8080/hello/10?name=admin`

9. application中对thymeleaf常用配置：

   ```yaml
   spring:
     thymeleaf:
       prefix: classpath:/templates/  # 视图解析器前缀
       suffix: .html  # 后缀
       mode: HTML  # 配置视图模板类型，如果视图模板使用的是html5需要配置该项:HTML5
       encoding: UTF-8 # 编码格式
       servlet:
         content-type: */* # 响应类型，默认*/*
       cache: false # 配置页面缓存，开发设置为false
       enabled: true
   ```

   
