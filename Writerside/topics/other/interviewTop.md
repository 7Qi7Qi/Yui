# InterviewTop

### Java方法冲突

1. 原因 
   1. 一个类可以实现多个接口，java8起可以定义静态方法，使用default关键字
   2. 可能会有相同签名的方法的实现，即方法冲突 
2. 如果发生方法冲突，java会调用优先级高的方法
   1. 总结：越具体越优先，越抽象的或离本类越远的优先级越低
   2. 类的优先级比接口高
   3. 子类的优先级比父类高
   4. 相同优先级
      1. 本类就必须重写方法，来显示的选择方法实现。 
      2. ``父类名.super.方法名()``
      3. 不指定，编译会出错


```java
public interface D extends A, B {
    @Override
    default void run() {
        System.out.print("D run");
    }
}
public class Son implements A, B, D {

    public static void main(String[] args) {
        new Son().run(); //打印 D run，优先调用子接口方法
    }
}
```

```java
public class Son implements A, B {

    public static void main(String[] args) {
        new Son().run();
    }
    
    @Override
    public void run() {
        A.super.run(); //打印 A run
    }
}
```


### 内存溢出和内存泄漏

内存溢出：程序在申请内存时，此时已用内存过多，没有足够的剩余内存供其使用
内存泄漏：程序在申请内存后，不能完全释放已申请的内存空间

### 不稳定排序算法
希尔排序、直接选择排序、堆排序、快速排序

### SQL优化方法
核心就是避免全表扫描，多走索引
1. 尽量对利用字段较多的建立索引，即在where及order by涉及的列上建立索引
2. 尽量避免在where字句中使用or、null值判断，in和 对字段使用函数操作
3. 建立索引时需要多考虑最左匹配原则

### 内核态和用户态
1. 为了避免操作系统和关键数据被用户程序破坏，将处理器的执行状态分为内核态和用户态
2. 内核态是操作系统管理程序执行时所处的状态，能够执行包含特权指令在内的一系列指令，能够访问系统内所有的存储空间
3. 用户态是用户程序执行时处理器所在的状态，不能执行特权指令，只能访问用户地址空间
4. 用户程序运行在用户态，操作系统内核运行在内核态

### 集合并行流中的Spring事务会生效吗？

1. parallelStream()：``ForkJoinPool.commonPool.worker``
2. 一般来说并行流会新建线程去执行方法，而Spring的事务只会保证主线程的事务会回滚，其他线程事务不会回滚
3. ThreadLocal实现的事务（保存的数据库连接）。 [详见](Spring.md#事务)
4. 所以不要再并行流中使用ThreadLocal、数据库事务