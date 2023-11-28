# JVM


## Tools

>  **jdk/bin目录下**。[参考链接](https://blog.csdn.net/youyou1543724847/article/details/84647552)


1. jar： jar包工具
2. javac：java程序编译器
3. javah：C头文件和stub文件生成器
4. javap：java反编译器
5. 监控工具 
   1. jvisualvm（gui监控工具） 
   2. jconsole（gui监控工具）
         1. java mbean对象
   3. jmc（java mission control）
   4. jmx
   5. java flight profile
   6. jhat
      1. java heap dump文件浏览器
7. jdk命令行监控工具
   1. jstack 生成jvm快照信息
   2. jps 查看当前Java进程
   3. jinfo 查看JVM配置信息
   4. jstat 显示虚拟机运行时各方面数据
      1. jvm statistics monitoring tool
      2. jvm内建指令对应用资源和性能进行实时的命令行监控
   5. jmap（java memory map） 内存使用情况监控，生成堆转存文件
8. jdb：java调试工具
9. 安全相关
   1. policy tool：gui策略工具
   2. keytool：密钥工具
   3. kinit、klist、ktab：kerberos管理工具
   4. jarsigner：签名、签名校验工具
10. rmiregistry

## OOM

> VM Option: -Xmx30m -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/Java/logs
> 