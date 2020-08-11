# Spring Security 实战

学习目的：

- 同时支持多种认证方式

- 同时支持多种认证渠道

- 支持集群环境，跨应用工作，SESSOION控制，控制用户权限，防护与身份认证相关攻击



涉及到的项目：

- Spring Security
- Spring Social
- Spring Security OAuth

![image-20191004101937677](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20191004101937677.png)

## Spring MVC Restful API

![image-20191015185756019](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20191015185756019.png)



![image-20191015190026446](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20191015190026446.png)

现在基本达到Level 2。





![image-20191015191546161](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20191015191546161.png)

![image-20191015191601572](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20191015191601572.png)

dto包一般用于RestController 传输对象。



jsonPath，很好用的json工具包。



@PathVariable 映射url片段到java方法的参数。



@jsonView使用

![image-20191016123938933](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20191016123938933.png)

```java
	@GetMapping("/{id:\\d+}")
	@JsonView(User.UserDetailView.class)
	public User getInfo(@ApiParam("用户id") @PathVariable String id) {
//		throw new RuntimeException("user not exist");
		System.out.println("进入getInfo服务");
		User user = new User();
		user.setUsername("tom");
		return user;
	}
```





@RequestBody



- 日期类型参数传递处理

  目前一般不使用带格式的日期传递。直接使用时间戳。

- 数据校验

  Hibernate Validator

  @Valid @NotBlank

  收集校验信息：BindingResult

  ```java
  	@PutMapping("/{id:\\d+}")
  	public User update(@Valid @RequestBody User user, BindingResult errors) {
  
  		if(errors.hasErrors()) {
  			errors.getAllErrors().stream().forEach(error -> System.out.println(error.getDefaultMessage()));
  		}
  
  		System.out.println(user.getId());
  		System.out.println(user.getUsername());
  		System.out.println(user.getPassword());
  		System.out.println(user.getBirthday());
  
  		user.setId("1");
  		return user;
  	}
  	
  ```

  常用的验证注解：

  ![image-20191016171215337](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20191016171215337.png)

![image-20191016172109697](/Users/zengxiangfei/Documents/mywiki/spring/images/image-20191016172109697.png)



自定义校验逻辑：





Restful 服务的错误处理：



## Spring Mvc处理其他web应用常见场景



