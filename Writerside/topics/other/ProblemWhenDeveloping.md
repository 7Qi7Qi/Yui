# 遇到问题

## Maven

### 1、Blocked mirror for repositories

#### 报错信息

```
maven-default-http-blocker (http://0.0.0.0/): Blocked mirror for repositories
```

> 原因：Maven升级3.8.1后，默认配置文件中增加了一组标签，仓库镜像是http，会被拦截禁止访问

#### 解决方法

1. 降低Maven版本到**3.8.1**以下

2. 增加节点

   ```xml
   <mirror>
       <id>insecure-repo</id>
       <mirrorOf>external:http:*</mirrorOf> 
       <url>http://www.ebi.ac.uk/intact/maven/nexus/content/repositories/ebi-repo/</url>
       <blocked>false</blocked>
   </mirror>
   ```
3. 将镜像仓库从http变成https
4. 注释掉$MAVEN_HOME/conf/settings.xml 中的拦截标签

   ```xml
   <mirror>
    <id>maven-default-http-blocker</id>
    <mirrorOf>external:http:*</mirrorOf>
    <name>Pseudo repository to mirror external repositories initially using HTTP.</name>
    <url>http://0.0.0.0/</url>
    <blocked>true</blocked>
    </mirror>
   ```



[参考链接]: https://xingyun.blog.csdn.net/article/details/119038072

