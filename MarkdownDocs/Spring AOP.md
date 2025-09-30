你提到 **AOP 注解**，那我给你系统梳理一下 **Spring AOP 常见的注解**和它们的作用。

---

## 🔹Spring AOP 常见注解

### 1. `@Aspect`

* 作用：声明一个切面类（Aspect）。
* 使用在类上，告诉 Spring 这个类里定义了切点和通知。

```java
@Aspect
@Component
public class LogAspect {
    ...
}
```

---

### 2. `@Pointcut`

* 作用：定义切点（切入位置的规则）。
* 使用在方法上，方法本身通常没有逻辑，只是一个命名的切点表达式。

```java
@Pointcut("execution(* com.example.service.*.*(..))")
public void serviceMethods() {}
```

---

### 3. 通知类注解（Advice）

#### （1）`@Before`

* 作用：在目标方法执行 **前** 执行。

```java
@Before("serviceMethods()")
public void beforeAdvice() {
    System.out.println("方法执行前记录日志");
}
```

#### （2）`@After`

* 作用：在目标方法执行 **后（无论是否异常）** 执行。

```java
@After("serviceMethods()")
public void afterAdvice() {
    System.out.println("方法执行后清理资源");
}
```

#### （3）`@AfterReturning`

* 作用：目标方法 **正常返回后** 执行，可以获取返回值。

```java
@AfterReturning(pointcut = "serviceMethods()", returning = "result")
public void afterReturningAdvice(Object result) {
    System.out.println("返回值: " + result);
}
```

#### （4）`@AfterThrowing`

* 作用：目标方法 **抛出异常时** 执行，可以获取异常。

```java
@AfterThrowing(pointcut = "serviceMethods()", throwing = "ex")
public void afterThrowingAdvice(Exception ex) {
    System.out.println("异常信息: " + ex.getMessage());
}
```

#### （5）`@Around`

* 作用：包裹整个方法，手动决定方法是否执行以及何时执行（最强大的通知）。

```java
@Around("serviceMethods()")
public Object aroundAdvice(ProceedingJoinPoint pjp) throws Throwable {
    System.out.println("方法执行前");
    Object result = pjp.proceed();  // 执行目标方法
    System.out.println("方法执行后");
    return result;
}
```

---

## 🔹示例：日志切面

```java
@Aspect
@Component
public class LogAspect {

    @Pointcut("execution(* com.example.service.*.*(..))")
    public void serviceMethods() {}

    @Before("serviceMethods()")
    public void logBefore() {
        System.out.println("调用方法之前记录日志");
    }

    @AfterReturning(pointcut = "serviceMethods()", returning = "result")
    public void logAfterReturning(Object result) {
        System.out.println("方法返回: " + result);
    }

    @AfterThrowing(pointcut = "serviceMethods()", throwing = "ex")
    public void logException(Exception ex) {
        System.out.println("捕获异常: " + ex.getMessage());
    }
}
```

---

👉 这些注解主要配合 **切点表达式**使用，形成一个完整的 AOP 流程。

要不要我帮你整理一份 **切点表达式写法总结**（例如 execution、within、@annotation 等），这样你对 AOP 注解会有更系统的理解？