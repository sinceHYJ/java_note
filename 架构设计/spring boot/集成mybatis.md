## 1. 加入相应的依赖

```xml
<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
</dependency>

<dependency>
  <groupId>org.mybatis.spring.boot</groupId>
  <artifactId>mybatis-spring-boot-starter</artifactId>
  <version>1.3.1</version>
</dependency>
```



## 2. 配置数据库连接信息

```yml
spring:
  application:
    name: consumer1


  # 配置mysql连接信息
  datasource:
    url: jdbc:mysql://39.107.70.222:3306/sakila
    username: root
    password: Since18!
    driver-class-name: com.mysql.cj.jdbc.Driver
```



## 3. 使用mybatis插件自动生成代码

### 3.1 pom文件引入插件

```xml
<plugin>
  <groupId>org.mybatis.generator</groupId>
  <artifactId>mybatis-generator-maven-plugin</artifactId>
  <version>1.3.4</version>
  <configuration>
    <verbose>true</verbose>
    <overwrite>true</overwrite>

  </configuration>
  <dependencies>
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.44</version>
    </dependency>
  </dependencies>
</plugin>
```

### 3.2 设置自动生成代码的xml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <!-- 通过maven插件的形式生成代码，目标mybatis-generator:generate -->
    <context id="context1" defaultModelType="flat">

        <jdbcConnection driverClass="com.mysql.jdbc.Driver"
                        connectionURL="jdbc:mysql://39.107.70.222:3306/sakila?characterEncoding=UTF8"
                        userId="root" password="Since18!"/>

        <!-- JavaTypeResolverGSXImpl对mysql到java的类型转换进行了个性化处理，日期类型转换为LocalDate，smallint、tinyint转换为Integer -->
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
            <!-- 时间类型是否使用LocalDateTime -->
            <property name="useLocalDateTime" value="false"/>
            <!-- 是否强制转换smallint和tinyint为Integer类型 -->
            <property name="forceInteger" value="true"/>
        </javaTypeResolver>

        <javaModelGenerator targetPackage="com.hyj.demo.mapper"
                            targetProject="src/main/java">
            <!-- 是否针对string类型的字段在set的时候进行trim调用 -->
            <property name="trimStrings" value="true"/>
        </javaModelGenerator>

        <!--生成mapper位置-->
        <sqlMapGenerator targetPackage="com.hyj.demo.mapper"
                         targetProject="src/main/java">
        </sqlMapGenerator>

        <javaClientGenerator targetPackage="com.hyj.demo.dao"
                             targetProject="src/main/java" type="XMLMAPPER">
        </javaClientGenerator>

        <!--<table tableName="activity_flow_evaluation" />-->
        <table tableName="city"></table>
    </context>
</generatorConfiguration>
```

**尽量把mapper.xml文件与mapper.java放入同样的文件夹中**

### 3.3 使用命令mybatis-generator:generate生成

使用该命令前必须添加相关插件。

#### 3.4 添加@MapperScan("com.hyj.demo.mapper")

## 遇到的问题

1. mybatis绑定错误-- Invalid bound statement (not found)

**解决办法：**

mapper.xml与mapper.java没有放到一个文件夹下。

也是google出来的答案。