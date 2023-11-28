# 其他

## 各种Object

> DO、DTO、BO、AO、VO、POJO 
>
> - DO （ Data Object ）：与数据库表结构一一对应，通过 DAO 层向上传输数据源对象。
> - DTO （ Data Transfer Object ）：数据传输对象，Service 或 Manager 向外传输的对象。
> - BO （ Business Object ）：业务对象。 由 Service 层输出的封装业务逻辑的对象。
> - AO （ Application Object ）：应用对象。 在 Web 层与 Service 层之间抽象的复用对象模型，极为贴近展示层，复用度不高。
> - VO （ View Object ）：显示层对象，通常是 Web 向模板渲染引擎层传输的对象。
> - POJO （ Plain Ordinary Java Object ）：在本手册中，POJO 专指只有 setter/getter/toString 的简单类，包括 DO/DTO/BO/VO 等。
>
> ![image-20220216221201297](image-20220216221201297.png)



## Runtime

### addShutdownHook

```java
Runtime.getRuntime().addShutdownHook(new Thread(() -> {
    //logic code
}))
```

> 注册一个JVM关闭的钩子，当系统执行完这些钩子，jvm才会关闭。可以执行功能：内存清理、对象销毁、关闭连接等操作
>
> 1. 程序正常退出
> 2. 使用System.exit()
> 3. 终端使用Ctrl+C触发中断
> 4. 系统关闭
> 5. OOM宕机
> 6. Kill pid 结束进程

## 运算符优先级

![运算符优先级](OperatorSymbolPriority.png)

> **单目乘除为关系，逻辑三目后赋值。** 
>
> 1. 单目：单目运算符+ –(负数) ++ -- 等 
> 2. 乘除：算数单目运算符* / % + - 
> 3. 为：位移单目运算符<< >> 
> 4. 关系：关系单目运算符> < >= <= == != 
> 5. 逻辑：逻辑单目运算符&& || & | ^ 
> 6. 三目：三目单目运算符A > B ? X : Y 
> 7. 后：无意义，仅仅为了凑字数 
> 8. 赋值：赋值=
