# Introduction

https://paper.pig4cloud.com/

- [ ] java gc
- [ ] **内存泄漏**
- [x] redis
- [ ] 多线程简单实现
- [ ] 复制计算机网络
- [ ] 看操作系统
- [ ] 有道云

//删除锁 分布式锁

幻读 脏读
淘汰策略
zset具体实现
适合创建索引
在使用到条件查询字段、表连接的字段、排序分组字段，可以创建索引,例如where、on、order by、group by。
创建索引需要注意以下的原则：
    列的离散度不能太低。
    列的数据不能更新太频繁。
    不建议使用身份证号、uuid等无序的数据列创建索引。
    一个表中创建索引的数量不能过多。
    尽可能的创建联合索引，而不是新增一个索引。
    字段过长时，使用前缀索引。


1. [网络故障](https://www.javashitang.com/md/java/%E7%94%9F%E4%BA%A7%E7%8E%AF%E5%A2%83%E5%8F%91%E7%94%9F%E9%97%AE%E9%A2%98%EF%BC%8C%E4%BD%A0%E4%B8%80%E8%88%AC%E6%80%8E%E4%B9%88%E6%8E%92%E6%9F%A5-%E7%BD%91%E7%BB%9C%E7%AF%87.html)
2. [垃圾回收](https://www.javashitang.com/md/java/%E8%AF%B4%E4%B8%80%E4%B8%8B%E5%B8%B8%E8%A7%81%E7%9A%84%E5%9E%83%E5%9C%BE%E6%94%B6%E9%9B%86%E5%99%A8%E5%8F%8A%E5%85%B6%E9%80%82%E7%94%A8%E5%9C%BA%E6%99%AF.html)
3. [Synchronized](https://www.javashitang.com/md/java/%E8%AF%B4%E4%B8%80%E4%B8%8BSynchronized%E5%BA%95%E5%B1%82%E5%AE%9E%E7%8E%B0%EF%BC%8C%E9%94%81%E5%8D%87%E7%BA%A7%E7%9A%84%E5%85%B7%E4%BD%93%E8%BF%87%E7%A8%8B.html)
4. [String类](https://www.javashitang.com/md/java/String%E7%B1%BB%E4%B8%BA%E4%BB%80%E4%B9%88%E8%A2%AB%E8%AE%BE%E8%AE%A1%E4%B8%BA%E4%B8%8D%E5%8F%AF%E5%8F%98%E7%9A%84.html)
5. [内存泄漏](https://www.javashitang.com/md/java/%E5%86%85%E5%AD%98%E6%B3%84%E6%BC%8F%EF%BC%8C%E5%86%85%E5%AD%98%E6%BA%A2%E5%87%BA%E5%A6%82%E4%BD%95%E6%8E%92%E6%9F%A5.html)
6. [故障排查 - linux](https://www.javashitang.com/md/java/%E7%94%9F%E4%BA%A7%E7%8E%AF%E5%A2%83%E5%8F%91%E7%94%9F%E9%97%AE%E9%A2%98%EF%BC%8C%E4%BD%A0%E4%B8%80%E8%88%AC%E6%80%8E%E4%B9%88%E6%8E%92%E6%9F%A5-Linux%E5%B7%A5%E5%85%B7%E7%AF%87.html)


基于OAuth2.0

dubbo
https://segmentfault.com/a/1190000019896723

https://blog.csdn.net/weixin_38004638/article/details/99655322



sharding-jdbc
https://github.com/allentofight/easy-cs/blob/main/Java/Sharding-JDBC%E7%9A%84%E5%9F%BA%E6%9C%AC%E7%94%A8%E6%B3%95%E5%92%8C%E5%9F%BA%E6%9C%AC%E5%8E%9F%E7%90%86.md

任务调度：


开发框架：Spring Boot 2.6
微服务框架：Spring Cloud 2021
安全框架：Spring Security + Spring OAuth 2.0
任务调度：Quartz 、 XXL-JOB
持久层框架：MyBatis Plus
数据库连接池：Druid
服务注册与发现: Nacos
客户端负载均衡：Spring Cloud Loadbalancer
熔断组件：Sentinel
网关组件：Spring Cloud Gateway
日志管理：Logback
运行容器：Undertow
分布式事务：LCN
工作流: Activiti 5.22