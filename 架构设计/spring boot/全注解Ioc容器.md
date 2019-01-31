# IOC容器

## bean注入

使用注解@Autowired注入相关bean，该注解默认按照类型（byType）进行注解。

当发生冲突时，spring会按照变量名进一步匹配bean。

为消除歧义性（相同类型有多个bean），可以有以下2种方式：

- 使用注解@Primary指定存在多个bean时，首先使用该bean注入，但若多个类使用了@Primary，则还是会发生冲突。
- 使用注解@Quelifier来进一步指定bean的名称，与@Autowired配合使用。

## 扫描装配bean

当存在多个bean需要spring管理时，我们可使用注解@ComponentScan来定义要扫描的包。

```java
@ComponentScan(excludeFilters = {
		@Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
```

当不指定要扫描的包时，**默认扫描该注解所在包及子包**

可以使用basePackageClasses定义扫描的类，其中还有includeFilters和excludeFilters定义满足过滤器（Filter）条件的bean才去扫描，或排除。

## @Component与@Bean

**有了注解```@Component```为什么还需要```@Bean```?**

我们自己写的代码可以使用注解@Component，将该类交由spring容器管理。而我们引用第三方jar包时，无法再在jar包内某类上加该注解，只能使用java配置类的方式，通过@Bean方式实例化jar包中某类并交由spring容器管理。

## 读取配置文件

spring默认会读取配置application.properties文件，文件内使用key=value的形式。我们可使用

- 注解```@Vlaue("${database.username}")```来引入其中的值。
- 当然我们可以使用注解```@ConfigurationProperties("database")```使配置上有所减少。**自动以前缀database去匹配配置文件中的key。**

例：

application.properties配置文件

```java
database.user=heyingjie
database.password=Sincehyj
database.url=45.25.35.36
```

配置类中：

```java
@Component
@ConfigurationProperties("database")
public class Database1 {

    private String user;
    private String password;
    private String url;

    public String getUser() {
        return user;
    }

    public void setUser(String user) {
        this.user = user;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public String getUrl() {
        return url;
    }

    public void setUrl(String url) {
        this.url = url;
    }

    public void close() {
        System.out.println("对象Dtabase1销毁！");
    }
}
```

## 条件装配bean

