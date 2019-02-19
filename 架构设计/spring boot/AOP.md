# AOP概念--切面编程

AOP核心技术位jdk动态代理，**但动态代理依赖的是接口，也就是全部的实现必须采用接口+实现类的方式。**

而spring还有另外一种方式实现：CGLIB。当有接口的时候采用动态代理，无接口时采用CGLIB。

Aop用来1:增强代码的功能，2:减少大幅重复的工作。

## AOP几大概念

- **连接点（Join point）**:对应的是具体被拦截的对象，因为spring只支持**方法**，所以一般指某类中的某方法。
- **切点（point cut）**：切面不仅可以应用于单个类的单个方法，还可应用与一些方法。我们可以使用正则表达式来定义，匹配规则。例```execution(* com.dance..UserSericeImpl.printUser(..)) && within(com.dance.test..*)```
- **通知（advice）**：前置通知，后置通知等，也就是拦截前后调用的方法。
- **引入（introduction）**：是指引入新的类或新的方法来增强原有的方法的功能。
- **织入（weaving）**：指通过动态代理技术，为原有服务对象生成对象，然后将与切点定义匹配的连接点拦截，并按照约定将各类通知织入约定流程的过程。
- **目标（target）**：即被代理的对象。指的是某个类的某个对象。

## 使用

我们可以使用注解```@Aspect```来定义一个切面实现类。例：

```java
@Aspect
@Component
public class MyAspect {

    @DeclareParents(value = "com.dance.test.service.UserSericeImpl",
            defaultImpl = UserValidatorImpl.class)
    public UserValidator userValidator;

    @DeclareWarning(value = "com.dance.test.service.UserSericeImpl")
    public String msg = "提示信息";

    @Pointcut("execution(* com.dance..UserSericeImpl.printUser(..)) && within(com.dance.test..*)")
    private void pointCut() {

    }

    @Before(value = "pointCut() && args(user)")
    private void befor(JoinPoint jp, User user) {
        Object[] args = jp.getArgs();
        for (int i = 0 ; i< args.length; i++) {
            System.out.println(args[i].toString());
        }
        System.out.println("前置通知:"+user.getUsername()+"/"+user.getPassword());
    }

    @After("pointCut()")
    private void after() {
        System.out.println("后置通知.....");
    }

    @AfterReturning("pointCut()")
    private void afterRunning() {
        System.out.println("返回通知.....");
    }

    @AfterThrowing("pointCut()")
    private void afterThrowing() {
        System.out.println("异常通知.....");
    }
}
```



## 匹配表达式

切入点指示符用来指示切入点表达式目的，在Spring AOP中目前只有执行方法这一个连接点，Spring AOP支持的AspectJ切入点指示符如下：

​         **execution****：**用于匹配方法执行的连接点；

​         ***within******：****用于匹配指定类型内的方法执行；*

​         ***this******：****用于匹配当前**AOP**代理对象类型的执行方法；注意是**AOP**代理对象的类型匹配，这样就可能包括引入接口也类型匹配；*

​         ***target******：****用于匹配当前目标对象类型的执行方法；注意是目标对象的类型匹配，这样就不包括引入接口也类型匹配；*

​         ***args******：****用于匹配当前执行的方法传入的参数为指定类型的执行方法；*

​         ***@within******：****用于匹配所以持有指定注解类型内的方法**；*

​         ***@target******：****用于匹配当前目标对象类型的执行方法，其中目标对象持有指定的注解；*

​         ***@args******：****用于匹配当前执行的方法传入的参数持有指定注解的执行；*

​         ***@annotation******：****用于匹配当前执行方法持有指定注解的方法；*

​         ***bean******：****Spring AOP**扩展的，**AspectJ**没有对于指示符，用于匹配特定名称的**Bean**对象的执行方法；*

​         ***reference pointcut******：****表示引用其他命名切入点，只有**@ApectJ**风格支持，**Schema**风格不支持。*

​       AspectJ切入点支持的切入点指示符还有： call、get、set、preinitialization、staticinitialization、initialization、handler、adviceexecution、withincode、cflow、cflowbelow、if、@this、@withincode；但Spring AOP目前不支持这些



**其中@args @target等表示注解，也就是用这些注解修饰的参数，返回类型等**

### 匹配语法

首先让我们来了解下AspectJ类型匹配的通配符：

- *：匹配任何数量字符；

- ..：匹配任何数量字符的重复，如在类型模式中匹配任何数量子包；而在方法参数模式中匹配任何数量参数。

-  +**：**匹配指定类型的子类型；仅能作为后缀放在类型模式后边。

  例如：

  ```java
  java.lang.String    匹配String类型；
  java.*.String       匹配java包下的任何“一级子包”下的String类型；
  如匹配java.lang.String，但不匹配java.lang.ss.String
  java..*            匹配java包及任何子包下的任何类型;
  				  如匹配java.lang.String、java.lang.annotation.Annotation
  java.lang.*ing      匹配任何java.lang包下的以ing结尾的类型；
  ```

> java.lang.Number+ <font color=green> 匹配java.lang包下的任何Number的自类型；
> 				   如匹配java.lang.Integer，也匹配java.math.BigInteger</font>

匹配的模式：

> 注解？ 修饰符? 返回值类型 类型声明?方法名(参数列表) 异常列表？

各部分解释：

- **注解：**可选，方法上持有的注解，如@Deprecated；
- **修饰符：**可选，如public、protected；
- **返回值类型：**必填，可以是任何类型模式；“*”表示所有类型；
- **类型声明：**可选，可以是任何类型模式；
- **方法名：**必填，可以使用“*”进行模式匹配；
- **参数列表：**“()”表示方法没有任何参数；“(..)”表示匹配接受任意个参数的方法，“(..,java.lang.String)”表示匹配接受java.lang.String类型的参数结束，且其前边可以接受有任意个参数的方法；“(java.lang.String,..)” 表示匹配接受java.lang.String类型的参数开始，且其后边可以接受任意个参数的方法；“(*,java.lang.String)” 表示匹配接受java.lang.String类型的参数结束，且其前边接受有一个任意类型参数的方法；
- **异常列表：**可选，以“throws 异常全限定名列表”声明，异常全限定名列表如有多个以“，”分割，如throws java.lang.IllegalArgumentException, java.lang.ArrayIndexOutOfBoundsException。

**我们在代码中可以使用&&、||、！表达与、或、非，但在xml文件使用使用and、or、not。**



## 引入：增强接口的功能

使用注解 **```@DeclareParents```**引入新的类来增强服务，他有2个参数：`value`，`defaultImpl`。

- value：指的是目标对象。
- defaultImpl：只要使用增强接口的class。

例上面代码。

**原理：**动态的为目标对象生成代理对象时，也实现了defaultImpl接口。



## 为通知注入参数

1. 非环绕型通知我们可使用 `JoinPoint`类中的方法 `Object[] args = jp.getArgs()`来获取参数。环绕型通知使用 `ProceedingJointPoint`来获取参数。
2. 还可使用类似前面全的代码 `before()`方法。正则是 `pointCut() && args(user)`表示启用原来定义切点的规则，**并且约定将连接点目标方法名称为user的参数传递进来。**

## 多个切面

若不指定顺序，切面的执行的顺序是 **混乱的**，我们可以使用以下的方法来指定顺序。

1. 使用注解 `@Order(1)`来指定。
2. 实现接口Ordered，实现方法getOrder() 方法。返回值为整数值。

当定义顺序后可以看到前置通知采用 **从小到大**顺序执行，后置通知等，采用 **从大到小**执行。这是一个典型的 **责任链模式**的顺序。

