## rest接口

rest，英文名：Representational State Transfer，表现层状态转移。重要的核心概念就是下面3部分：

     Resource：资源，即数据。
     Representational：某种表现形式，比如用JSON，XML，JPEG等；
     State Transfer：状态变化。通过HTTP动词实现

其实定义了接口的设计形式，1. 请求路径的设计为以资源为中心。2. 表现形式为请求json、或返回json。3.状态转移即增加、删除、更新与删除等。

## 常用注解

```@RestContoller```表现为请求、及返回都设置为json格式。

```@GetMapping```其中的一个变量是```String[] produces() default {};``，限制请求参数的类型：

```java
produces = "text/plain"
produces = {"text/plain", "application/*"}
produces = "application/json; charset=UTF-8"
```



一般情况下接口返回的状态码为200，而对于返回类型，我们可以使用类```ResponseEntity```来包装返回类型。

也可以使用注解```@ResponseStatus```指定返回值的状态码。

## RestTemplate

spring中的restTmplate类使用封装了常用的rest请求方式。