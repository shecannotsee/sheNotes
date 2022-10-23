## 1.创建一个maven工程

## 2.添加springboot依赖，在pom.xml中添加依赖

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.14.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

## 3.编写启动程序

EssaysApplication.java

```java
package com.shecannotsee;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * @SpringBootApplication标注主程序启动，说明是一个springboot应用
 */
@SpringBootApplication
public class EssaysApplication
{
    public static void main(String[] args){

        //Spring应用启动
        SpringApplication.run(EssaysApplication.class,args);
    }
}

```



## 4.编写相关业务逻辑

PrintfController.java

```java
package com.shecannotsee.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class PrintfController
{
    @ResponseBody//方法返回的数据直接写给浏览器，如果是对象，可转为json数据
    @RequestMapping("/hello")//接受浏览器发送过来的指令
    public String Printf(){
        String temp="hello world";
        return temp;
    }
}
```

@RestController：就是@Controller加上@ResponseBody

## 5.测试

略

## 6.打包发布,在pom.xml中添加依赖

```xml
<!--该插件可将项目打包成jar包一键部署-->
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <version>1.5.14.RELEASE</version>
        </plugin>
    </plugins>
</build>
```

maven插件-声明周期-点击package，执行打包，在windows下使用命令，假设包名为xxxxx.jar

```cmd
java -jar xxxxx.jar
```



## 7.对于程序启动的解析

### 1.spring-boot-starter-parent

pom.xml里面的依赖

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.14.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```

对于上述父项目，其还有一层父项目，该父项目里包含了主要依赖，包含了springboot版本中心管理

```xml
<parent>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-dependencies</artifactId>
	<version>1.5.14.RELEASE</version>
	<relativePath>../../spring-boot-dependencies</relativePath>
</parent>
```

### 2.spring-boot-starter-web

pom.xml里面的依赖

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

上述依赖是一个springboot场景启动器：导入web模块正常运行所需要的组件，包括tomcat，springmvc，web等等。也可以根据需要导入其他木块，包括但不仅限于redis，jpa，websocket，mail等等。

### 3.@SpringBootApplication

标记springboot的启动入口

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {
    ...
}
```

#### @SpringBootConfiguration：springboot配置类

#### @EnableAutoConfiguration：开启自动配置功能

