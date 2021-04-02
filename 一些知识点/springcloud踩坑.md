### 1. jdk1.8以上不能使用内嵌Tomcat

> 原因: JDK1.8 以上的版本, 以下几个包以及被移除, 需要手动添加pom
>
> 错误信息: 
>
> ```
> org.springframework.context.ApplicationContextException: Unable to start web server; nested exception is org.springframework.boot.web.server.WebServerException: Unable to start embedded Tomcat
>     at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.onRefresh(ServletWebServerApplicationContext.java:155) ~[spring-boot-2.0.4.RELEASE.jar:2.0.4.RELEASE]
>     at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:544) ~[spring-context-5.0.8.RELEASE.jar:5.0.8.RELEASE]
>     at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.refresh(ServletWebServerApplicationContext.java:140) ~[spring-boot-2.0.4.RELEASE.jar:2.0.4.RELEASE]
>     at org.springframework.boot.SpringApplication.refresh(SpringApplication.java:762) [spring-boot-2.0.4.RELEASE.jar:2.0.4.RELEASE]
>     at org.springframework.boot.SpringApplication.refreshContext(SpringApplication.java:398) [spring-boot-2.0.4.RELEASE.jar:2.0.4.RELEASE]
>     at org.springframework.boot.SpringApplication.run(SpringApplication.java:330) [spring-boot-2.0.4.RELEASE.jar:2.0.4.RELEASE]
>     at org.springframework.boot.SpringApplication.run(SpringApplication.java:1258) [spring-boot-2.0.4.RELEASE.jar:2.0.4.RELEASE]
>     at org.springframework.boot.SpringApplication.run(SpringApplication.java:1246) [spring-boot-2.0.4.RELEASE.jar:2.0.4.RELEASE]
>     at com.example.cloud.eurekaservice.EurekaServiceApplication.main(EurekaServiceApplication.java:13) [classes/:na]
> 
> ```
>
> 解决方案: 重新加入包
>
> ```xml
> <dependency>
>         <groupId>com.sun.xml.bind</groupId>
>         <artifactId>jaxb-core</artifactId>
>         <version>2.3.0.1</version>
>     </dependency>
>     <dependency>
>         <groupId>javax.xml.bind</groupId>
>         <artifactId>jaxb-api</artifactId>
>         <version>2.3.1</version>
>     </dependency>
>     <dependency>
>         <groupId>com.sun.xml.bind</groupId>
>         <artifactId>jaxb-impl</artifactId>
>         <version>2.3.1</version>
>     </dependency>
> ```
>
> 

