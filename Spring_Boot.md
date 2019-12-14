

# Spring Boot 简介

**1**.**Spring Boot 简化Spring应用开发**，约定大于配置，去繁从简，just run就能创建一个独立的，产品级别的应用，

## 优点

1.快速创建独立运行的Spring项目以及与主流框架集成------快

2.使用嵌入式的Servlet容器，应用无需打成WAR包

3.starters自动依赖与版本控制

4.大量的自动配置，简化开发，也可修改默认值

5.无需配置XML，无代码生成，开箱即用

6.准生产环境的运行时应用监控

7.与云计算的天然集成

# 整合Springboot+Mybatis+Freemarker项目

链接--https://www.bysocket.com/technique/1610.html

## mysql数据库创建

### 1.创建数据库 springboot：

```
CREATE DATABASE springboot;
```

### 2.创建表 resllists

```
DROP TABLE IF EXISTS  `resllists`;
CREATE TABLE `resllists` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT '城市编号',
  `province_id` int(10) unsigned  NOT NULL COMMENT '省份编号',
  `city_name` varchar(25) DEFAULT NULL COMMENT '城市名称',
  `description` varchar(25) DEFAULT NULL COMMENT '描述',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
```

### 3.插入数据

```
INSERT resllist VALUES (1 ,1,'P城','我的老家   就住在这个屯。');
```

## 2.springboot项目目录

> com.integration.controller – Controller 层
>
> com.integration.dao – 数据操作层 DAO
>
> com.integration.beans – 实体类
>
> com.integration.service – 业务逻辑层
>
> IntegrationApplication– 应用启动类
>
> application.properties – 应用配置文件，应用启动会自动读取配置

![springboot-创建项目的目录](C:\Users\wangrunfa\Desktop\Record\images\springboot项目目录.PNG)

## application配置—spring boot 应用启动类

```java
// Spring Boot 应用的标识
@SpringBootApplication
// mapper 接口类扫描包配置 扫描Dao包
@MapperScan("com.integration.dao")
public class Application {

    public static void main(String[] args) {
        // 程序启动入口
        // 启动嵌入式的 Tomcat 并初始化 Spring 环境及其各 Spring 组件
        SpringApplication.run(Application.class,args);
    }
}
```

## Controller控制层

```java


//@RestController注解包括了@Controller和@ResponseBody，
//@Controller 返回 指定页面类型
//@ResponseBody 返回的是json等数据类型
@Controller
public class RestController {
//@Autowired 对类成员变量、方法及构造函数进行标注，完成自动装配的工作
    @Autowired
    private integService integServices;
    //@RequestMapping 注解映射请求路径
    @RequestMapping
    public String ones(ModelMap model){
        resllist res=integServices.integDaosList();
        System.out.println(res);
        model.put("hellos",res);

        return "log";
    }

}
```

## Service逻辑层

### service

```java
public interface integService {
    resllist integDaosList();
}
```

### serviceImpl

```
public class integServiceImpl implements integService {

    @Autowired
    private integDao integDaos;

    @Override
    public resllist integDaosList() {
        return integDaos.integDao();
    }
}
```



## Dao数据访问层

```java
public interface integDao {
    
    resllist integDao();
}
```

## Bean类

```java
public class resllist {

    /**
     * id
     */
    private Long id;

    /**
     * 编号
     */
    private Long provinceId;

    /**
     * 城市
     */
    private String cityName;

    /**
     * 描述---
     */
    private String description;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public Long getProvinceId() {
        return provinceId;
    }

    public void setProvinceId(Long provinceId) {
        this.provinceId = provinceId;
    }

    public String getCityName() {
        return cityName;
    }

    public void setCityName(String cityName) {
        this.cityName = cityName;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }
    
    @Override
    public String toString() {
        final StringBuilder sb = new StringBuilder("{");
        sb.append("\"id\":")
                .append(id);
        sb.append(",\"provinceId\":")
                .append(provinceId);
        sb.append(",\"cityName\":\"")
                .append(cityName).append('\"');
        sb.append(",\"description\":\"")
                .append(description).append('\"');
        sb.append('}');
        return sb.toString();
    }
}
```

## integDao.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.integration.dao.integDao">
    <resultMap id="BaseResultMap" type="com.integration.beans.resllist">
        <result column="id" property="id" />
        <result column="province_id" property="provinceId" />
        <result column="city_name" property="cityName" />
        <result column="description" property="description" />
    </resultMap>

   <sql id="Base_Column_List">
      id, province_id, city_name, description
   </sql>

   <select id="findByName" resultMap="BaseResultMap">
      select
      <include refid="Base_Column_List" />
      from city
      where id = 1
   </select>
   <select id="integDao" resultMap="BaseResultMap">
      select
      <include refid="Base_Column_List" />
      from resllists
      where id = 1
   </select>
</mapper>
```

## applicetion.properties

```properties
## 数据源配置
spring.datasource.url=jdbc:mysql://localhost:3306/springboot?useUnicode=true&characterEncoding=utf8&useSSL=false
spring.datasource.username=root
spring.datasource.password=123456
spring.datasource.driver-class-name=com.mysql.jdbc.Driver

## Mybatis 配置
mybatis.mapper-locations=classpath:inMapper/*.xml
mybatis.type-aliases-package=com.integration.beans

## Freemarker
spring.freemarker.allow-request-override=false
spring.freemarker.cache=false
spring.freemarker.check-template-location=true
spring.freemarker.charset=UTF-8
spring.freemarker.content-type=text/html
spring.freemarker.expose-request-attributes=false
spring.freemarker.expose-session-attributes=false
spring.freemarker.expose-spring-macro-helpers=false
spring.freemarker.suffix=.html

##静态资源路径
spring.mvc.static-path-pattern=/static/**

##访问端口号
server.port=8083
```

## pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.integration</groupId>
    <artifactId>integration</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>integration</name>
    <description>Demo project for Spring Boot</description>

    <!-- Spring Boot 启动父依赖 -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.3.RELEASE</version>
    </parent>

    <properties>
        <mybatis-spring-boot>1.2.0</mybatis-spring-boot>
        <mysql-connector>5.1.39</mysql-connector>
    </properties>

    <dependencies>

        <!-- Spring Boot Web 依赖 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- Spring Boot Test 依赖 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

        <!-- Spring Boot Mybatis 依赖 -->
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>${mybatis-spring-boot}</version>
        </dependency>

        <!-- MySQL 连接驱动依赖 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>${mysql-connector}</version>
        </dependency>

        <!-- Junit -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-freemarker</artifactId>
        </dependency>
    </dependencies>
</project>

```

## templates/log.html页面

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h1>12356</h1>
<h2>${hellos}</h2>
</body>
</html>

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Spring Boot + Freemarker + Mybatis</title>
</head>
<body>
<h1>Hello 【 Spring Boot + Freemarker + Mybatis 】</h1>
<h2>Mybatis 数据：${hellos}</h2>
</body>
</html>
```

