# Spring Boot进行文件上传

### 静态页面

```html
<form action="/fileUpload" method="post" enctype="multipart/form-data">
    <label for="file">文件上传：</label>
    <input id="file" type="file" name="file">
    <input type="submit" value="上传">
</form>
```

### Controller

```java
    @PostMapping("/fileUpload")
    public String fileUpload(@NotNull MultipartFile file) throws IOException {
        System.out.println(file.getOriginalFilename());
        file.transferTo(new File("D:\\"+file.getOriginalFilename()));
        return "文件上传成功！";
    }
```

### 配置application.yml

```yaml
spring:
  servlet:
    multipart:
      max-file-size: 10MB #单个上传文件大小的限制
      location: C:\\ #文件上传存放位置
      enabled: true #是否允许文件上传  
      max-request-size: 20MB #一次请求上传文件的总容量的限制
```

