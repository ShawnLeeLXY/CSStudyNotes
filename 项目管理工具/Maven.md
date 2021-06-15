# Maven

## 第1节 Maven简介

### 1 简介

Maven是Apache组织中的一个开源项目

常见项目管理工具：

- Ant构建：XML脚本编写格式让XML文件特别大
- Maven：使用Java编写
- Gradle：使用DSL格式的配置文件



### 2 Maven的四大特性

依赖管理系统

project

Coordination（坐标）：

- groupId
- artifactId
- version

```xml
<dependency>
    <groupId>javax.servlet</groupId> com.baidu
    <artifactId>javax.servlet-api</artifactId> ueditor echarts
    <version>3.1.0</version>
</dependency>
```

POM文件格式示例：

```xml
<project xmlns = "http://maven.apache.org/POM/4.0.0"
    xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation = "http://maven.apache.org/POM/4.0.0
    http://maven.apache.org/xsd/maven-4.0.0.xsd">
 
    <!-- 模型版本 -->
    <modelVersion>4.0.0</modelVersion>
    <!-- 公司或者组织的唯一标志，并且配置时生成的路径也是由此生成， 如com.companyname.project-group，maven会将该项目打成的jar包放本地路径：/com/companyname/project-group -->
    <groupId>com.companyname.project-group</groupId>
 
    <!-- 项目的唯一ID，一个groupId下面可能多个项目，就是靠artifactId来区分的 -->
    <artifactId>project</artifactId>
 
    <!-- 版本号 -->
    <version>1.0</version>
</project>
```





## 第2节 Maven的安装配置

### 1 Maven的安装配置

[Maven官网下载](https://maven.apache.org/download.cgi)

环境变量-系统变量添加MAVEN_HOME
Path添加%MAVEN_HOME%

检查是否配置成功：DOS命令行输入`mvn -v`即查看版本

参考[Maven环境配置](https://www.runoob.com/maven/maven-setup.html)



### 2 Maven的目录结构

| 目录                          | 目的                           |
| ----------------------------- | ------------------------------ |
| ${basedir}                    | 存放pom.xml和所有子目录        |
| ${basedir}/src/main/java      | 项目的Java源代码               |
| ${basedir}/src/main/resources | 项目的资源，比如说property文件 |
| ${basedir}/src/test/java      | 项目的测试类，比如说JUnit代码  |
| ${basedir}/src/test/resources | 测试使用的资源                 |

修改maven\conf\settings.xml

```xml
<!--本地仓库地址：存放jar包-->
  <localRepository>D:/Develop/repository</localRepository>

<mirros>
    <!--更换阿里镜像，加快依赖下载-->
	 <mirror>
      <id>nexus-aliyun</id>
      <mirrorOf>central</mirrorOf>
      <name>Nexus aliyun</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public</url>
    </mirror>
</mirros>
```





## 第3节 Maven命令

### 1 Maven的编译和运行

编译java文件：`mvn compile`（第一次可能需要的时间比较长，因为要下载各种jar包）

执行main方法：`mvn exec:java -Dexec.mainClass="com.xxx.demo.Hello"`



### 2 常用命令

`mvn -version` 显示版本信息

`mvn clean `清理项目产生的临时文件，一般是模块下的target目录

`mvn compile`编译源代码，一般是模块下的target目录

`mvn test` 测试命令，或执行src/test/java/下junit的测试用例

*运行maven命令的时候，首先需要定位到maven项目的目录，也就是项目的pom.xml文件所在的目录，否则需要通过参数来指定项目的目录*

`-D` 传入属性参数

`-P` 使用指定的Profile配置





## 第4节 Maven项目的创建

### 1 IDEA集成Maven环境

*File*--->*New Projects Settings*--->*Settings for New Projects...*--->*Maven*



### 2 创建Java项目

quistart



### 3 创建Web项目

webapp

服务器的支持：

1. Jetty
2. Tomcat

Maven添加Tomcat，修改pom.xml：

```xml
<plugins>
      <plugin>
        <groupId>org.apache.tomcat.maven</groupId>
        <artifactId>tomcat7-maven-plugin</artifactId>
        <version>2.2</version>
        <configuration>
          <port>8080</port>
          <path>/web</path>
          <uriEncoding>UFT-8</uriEncoding>
          <server>tomcat7</server>
        </configuration>
      </plugin>
</plugins>
```





## 第5节 Maven仓库的基本概念

### 1 中央仓库

Maven仓库分为两类：**本地仓库**和**远程仓库**

Maven先在本地仓库找需要的jar包，如果没找到则去远程仓库找，仍然没找到则报错

远程仓库分为三种：**中央仓库**，**私服**，**其他公共库**

中央仓库是一个默认的远程仓库，包含2000多个开源项目



### 2 私服

私服是一种架设在局域网内的仓库服务，代理广域网上的远程仓库，供局域网内的Maven用户使用

需要配置pom.xml



### 3 其他公共库

常用的如阿里云Maven仓库，需要在pom.xml修改镜像地址





## 第6节 Maven环境下构建多模块项目

### 1 多模块

| 模块名           | 说明                                |
| ---------------- | ----------------------------------- |
| maven_parent     | 基模块，就是常说的parent（pom）     |
| maven_dao        | 数据库的访问层，例如JDBC操作（jar） |
| maven_service    | 项目的业务逻辑层（jar）             |
| maven_controller | 用来接受请求，响应数据（war）       |



创建多模块项目的步骤：

1. 创建Maven_parent项目，在IDEA中新建项目不选择模板
2. 创建Module，模板选择quickstart，名称设置为maven_dao
3. 同样的步骤创建maven_service模块
4. 创建maven_controller模块，模板选择webapp
5. 修改pom.xml，举例：
   - 修改jdk版本从1.7改为1.8
   - 修改junit版本从4.11改为4.12
   - 删除`<pluginManagement>`
6. 设置模块之间的依赖（dependency）
   - dao依赖parent
   - service依赖dao
   - controller依赖service，并引入Servlet的API
7. 在controller/src/main/下创建java目录并标记为*Sources Root*
8. 在java目录下创建包并创建一个类继承抽象类HttpServlet，重写`service()`方法
9. 为每个模块*Edit Configurations...*，包括添加tomcat，设置install和run命令





## 第7节 Maven的打包操作和依赖

### 1 Maven的打包操作

建立对应的目录结构

添加Profile配置

设置资源文件配置

执行打包操作



### 2 Maven依赖的基本概念

依赖的基本配置

依赖范围

传递性依赖