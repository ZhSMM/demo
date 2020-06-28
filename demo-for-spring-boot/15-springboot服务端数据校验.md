# Spring Boot服务端数据校验

Spring Boot使用Hibernate-validator 做数据校验。

### 对实体对象的校验

1. 修改实体类，在其属性上添加校验注解：
    * @NotNull : 对基本数据类型的对象类型做非空校验
    * @NotBlank ： 对字符串类型做非空校验
    * @NotEmpty ：对集合类型做非空校验
    * @Length(min= ,max= )：判断字符的长度（最大或最小）
    * @Min：判断数值最大值
    * @Max：判断数值最大值
    * @Email：判断邮箱是否合法
    
2. 在Controller中开启校验：没存在一个被校验的参数，就多一个BindingResult

    ```java
        @PostMapping("/addUser")
        public String addUser(@Validated User user, BindingResult result){
            if (result.hasErrors()){
                List<ObjectError> list=result.getAllErrors();
                for (ObjectError objectError : list) {
                    FieldError fieldError=(FieldError) objectError;
                    String fieldName= fieldError.getField();
                    String msg = fieldError.getDefaultMessage();
                    System.out.println("fieldName + msg = " + fieldName+" ---- " + msg);
                }
                return "addUser";
            }
            try {
                userService.addUser(user);
            }catch (Exception e){
                e.printStackTrace();
                return "error";
            }
            return "redirect:/success";
        }
    ```

3. 在页面中获取错误信息：

     ```html
        <form th:action="@{ /user/addUser }" method="post">
            用户姓名：
            <input type="text" name="username"><span th:errors="${user.username}"/><br/>
            用户性别：
            <input type="text" name="usersex"><span th:errors="${user.usersex}"/><br/>
            <input type="submit" value="提交">
        </form>
     ```

#### 自定义提示信息

方法一：

- 在字段校验的注解上添加信息：@NotNull(message = "xx不能为空")

方法二：

在配置文件中定义提示信息：配置文件名必须是 `ValidationMessages.properties`，放到resources目录下。

properties使用的是ascii编码，无法识别中文，需要转换为Unicode，可以使用jdk/bin目录下的native2ascii.exe进行转换：

- Windows控制台复制native2ascii.exe：选中后按enter键
- 输入native2ascii.exe运行，然后输入中文，回车

配置文件使用步骤：

1.  `ValidationMessages.properties`中：

   ```properties
   userid.notnull=\u7528\u6237id\u4e0d\u80fd\u4e3a\u7a7a-pro
   username.notnull=\u7528\u6237\u540d\u4e0d\u80fd\u4e3a\u7a7a-pro
   usersex.notnull=\u7528\u6237\u6027\u522b\u4e0d\u80fd\u4e3a\u7a7a-pro
   ```

2. 在需要校验的实体属性上添加校验注解：本质上就是属性注入实现

   ```java
       @NotNull(message = "{userid.notnull}")
       private Integer userid;
       @NotBlank(message = "{username.notnull}")
       private String username;
       @NotBlank(message = "{usersex.notnull}")
       private String usersex;
   ```

#### 改变传递的key

在Controller的入参上添加@ModelAttribute 注解修改 key，默认是入参类型的小写驼峰类型。

### 对其它参数做校验

在Controller上添加@Validated，然后在参数前添加校验注解@NotBlank

当参数不符合时会抛出异常 ConstrainViolationException，可以在全局异常处理类中进行处理。

