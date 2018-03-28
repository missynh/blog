---
title: 消息转换器HttpMessageConverter  
date: 2018-03-28  
tags: HttpMessageConverter
---
 问：为什么springMVC可以接收和返回json、xml格式？
 <!-- more -->
 在springMVC的设计中，HttpMessageConverter接口扮演着重要的角色，我们知道springMVC可以接收不同的消息形式，也可以将不同的消息形式响应回去(例如json格式)，那么各种不同的消息转换器，都会生成同样的转换结果。至于各种消息间解析细节的不同，就被屏蔽在不同的HttpMessageConverter实现类中了。  
 HttpMessageConverter<T>是Spring3.0新添加的一个接口，负责将请求信息转换为一个对象(类型为T)，将对象(类型为T)输出为响应信息
 
 #### HttpMessageConverter运行原理
  <img src="http://oo8ieb5e5.bkt.clouddn.com/image/vps/HttpMessageConverter.png" align="center" />
  
1. HttpInputMessage 将请求的信息先转为InputStream 对象，InputStream再由 HttpMessageConverter转换为 SpringMVC 需要的java对象  
2. SpringMVC 返回一个 java 对象， 并通过 HttpMessageConverter 转为响应信息，接着 HttpOutputMessage 将响应的信息转换为 OutputStream，接着给出响应。

#### HttpMessageConverter实现类
StringHttpMessageConverter：将请求信息转换为字符串  
FormHttpMessageConverter：将表单数据读取到MultiValueMap中  
ResourceHttpMessageConverter：读写org.springframework.core.io.Resource对象  
....

#### SpringMVC默认加载的HttpMessageConverter  
（1）DispatcherServlet默认加载HttpMessageConverter的6个实现类  
  DispatcherServlet默认装配RequestMappingHandlerAdapter，而RequestMappingHandlerAdapter默认装配：ByteArrayHttpMessageConverter、StringHttpMessageConverter、ResourceHttpMessageConverter、SourceHttpMessageConverter、AllEncompassingFormHttpMessageConverter、Jaxb2RootElementHttpMessageConverter  
（2）加入 jackson jar包后，启动的时候加载 7个 HttpMessageConverter 的实现类
  在上述6个的基础上，增加一个MappingJackson2HttpMessageConverter
  
#### 使用HttpMessageConverter  
  使用HttpMessageConverter<T>将请求信息转化并绑定到处理方法的入参中或将响应结果转换为对应的相应信息，Spring提供了两种途径：
- 使用@RequestBody/@ResponseBody对处理方法进行标注
- 使用HttpEntity<T>/ResponseEntity<T>作为处理方法的入参或返回值
> 当控制器处理方法使用到@RequestBody/@ResponseBody或者HttpEntity<T>/ResponseEntity<T>时，Spring首先根据请求头或者响应头的Accept属性选择匹配的HttpMessageConverter，进而根据参数类型或者泛型的过滤得到匹配的HttpMessageConverter；若找不到可用的HttpMessageConverter将报错

