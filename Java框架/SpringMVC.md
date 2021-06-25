# SpringMVC

## 一 特点

一个Spring内置的MVC框架

> MVC框架，它解决WEB开发中常见的问题(参数接收、文件上传、表单验证、国际化等等)，而且使用简单，与Spring无缝集成。支持 RESTful风格的URL请求。
>
> MVC模式（Model-View-Controller）：解决页面代码和后台代码的分离。

松散耦合可插拔的组件结构--->更具扩展性和灵活性





## 二 原理

SpringMVC对Servlet进行了深层次的封装

### MVC模式

> MVC分别是：模型model(javabean)、视图view(jsp/img)、控制器Controller(Action/servlet)。
>
> C存在的目的就是为了保证M和V的一致性，当M发生改变时，C可以把M中的新内容更新到V中。



### SpringMVC的执行流程

图

涉及组件分析





## 三 开发模式

### xml配置文件开发

导包

开发后端控制器（实现Controller接口）

配置springmvc.xml

开发View

配置前端控制器DispatcherServlet



### 注解开发

导包

开发后端控制器（添加@Controller注解，在方法上添加@RequestMapping注解以胚珠方法映射的uri）

配置springmvc.xml（配置包扫描）

开发View

配置前端控制器DispatcherServlet





## 四 接受请求

### @RequestMapping

`RequestMapping("/xxx")`

加在类上或方法上

有请求层次

#### 对请求的限制

方法限制

`RequestMapping(value="/xxx", method=RequestMethod.POST)`

参数限制

`RequestMapping(value="/xxx", params={"username", "password"})`



SpringMVC的方法默认可以注入 JavaWeb开发常用的数据共享对象

HttpServletRequest

HttpServletResponse

HttpSession





## 五 数据绑定

接收参数的方法：

1. request.getParameter()
2. 前后台同名匹配
3. 前后台不同名：`(@RequestParam("name") String username, ...)`
4. 接受数组：同名
5. 集合参数：`javaBean`
6. 封装成Map集合



SpringMVC默认接受的参数是ISO-8859-1编码参数，不支持中文，需要在配置文件中配置过滤器`<filter>`，设置编码方式为utf-8





## 六 共享数据

### 响应数据

响应前台数据的5种方式：

1. 利用HttpServletRequest、HttpServletResponse、HttpSession

2. ModelAndView对象的addObject()及setViewName()等方法，返回ModelAndView对象

3. Model对象的addAttribute()等方法，返回值是String类

   请求转发：返回值前加上`"forward:"`

   重定向：返回值前加上`"redirect:"`

4. 配置视图解析器，设置前缀和后缀，可以将跳转页面的uri简化为文件名

5. 返回POJO对象，可以使用`@ModelAttribute`或配置视图解析器实现



POJO（Plain Ordinary Java Objects）：简单的Java对象

WEB-INF目录下的资源，只能通过请求转发访问



### 转换JSON数据

SpringMVC默认使用的是 jackson作为对象转json的工具

转换步骤：

1. 设置`<mvc:annotation-driven/>`
2. 导入jackson的依赖
3. 标注`@ResponseBody`到JSON转换的方法上

SpringMVC自动地将对象或集合转换为JSON字符串返回给ajax请求方





## 七 文件上传与下载

### 上传文件

前台页面上传：Apache的Commons-FileUpload组件

后台接受上传：SpringMVC的MultipartFile对象

必须保证后台参数的名称和表单提交的文件名称一致



### 下载文件

JavaWeb开发中使用ServletOutStream向浏览器响应数据



### 拦截器

SpringMVC的拦截器：Interceptor

使用步骤：

1. 实现接口HandlerInterceptor
2. 在springmvc.xml中配置拦截器

重写HandlerInterceptor中的方法：

- preHandle：控制器方法执行之前执行（返回false拦截，true放行）
- postHandle：控制器方法执行后，视图渲染之前执行
- afterCompletion：视图渲染之后执行



### Controller的生命周期

Request：在用户的一次请求中生效多例

Session：Controller对象在一次会话中创建一个对象