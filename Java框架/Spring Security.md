# Spring Security

## 1 简介

Spring Security是一个为Java应用程序提供身份**认证**和**授权**的框架，**可扩展**

[官网手册](https://spring.io/projects/spring-security)



Spring Security底层有11个Filter（满足JavaEE规范）用来拦截和处理不同的请求，如认证、授权等

**Filter** ---> Spring MVC {**DispatcherServlet** ---> **Interceptor** ---> **Controller**}

