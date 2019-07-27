# SpringMVC

![](image/Xnip2019-02-27_17-29-45.png)

## spring MVC工作原理

- 1.spring mvc请所有的请求都提交给DispatcherServlet,它会委托应用系统的其他模块负责负责对请求进行真正的处理工作。 

- 2.DispatcherServlet查询一个或多个HandlerMapping,找到处理请求的Controller.

- 3.DispatcherServlet请请求提交到目标Controller

- 4.Controller进行业务逻辑处理后，会返回一个ModelAndView

- 5.Dispathcher查询一个或多个ViewResolver视图解析器,找到ModelAndView对象指定的视图对象 

- 6.视图对象负责渲染返回给客户端。 

## SpringMvc的控制器是不是单例模式,如果是,有什么问题,怎么解决？

答：是单例模式,所以在多线程访问的时候有线程安全问题,不要用同步,会影响性能的,解决方案是在控制器里面不能写字段。

![](image/Xnip2019-02-27_17-33-35.png)


## 简单的谈一下springmvc的工作流程

###  处理方式一：将方法的返回值设置为ModelAndView

1、创建ModelAndView独享
2、设置模型数据，最终会放大搜request域中
3、设置视图

### 处理方式二：方法的返回值仍是string类型，在方法的入参中传入Map、Model或者ModelMap


### 总结

不管将处理器方法的返回值设置为ModelAndView还是在方法的入参中传入Map、Model或者ModelMap，springMVC都会转换为一个ModelAndView对象


### 流程

![](image/Xnip2019-02-27_20-16-05.png)

1、向springmvc发送请求时，由在web.xml配置的DispatcherServlet拦截请求，由HandlerMapping映射调用对应的处理器进行处理。

