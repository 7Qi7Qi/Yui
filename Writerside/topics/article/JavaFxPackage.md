# JavaFx打包

## SpringBoot + JavaFx 实现

Spring启动类

```java
@SpringBootApplication
public class BootStrapApplication implements CommandLineRunner {

    public static void main(String[] args) {
        ConfigurableApplicationContext ctx = SpringApplication.run(BootStrapApplication.class, args);
            ctx.close();
    }

    @Override
    public void run(String... args) {
        // start java fx
        Application.launch(JavaFxApp.class, args);
    }
}
```



## javafxpackager

> JavaFx提供的打包工具，从jdk1.7就包含这个工具了，该命令既可以打包成jar也可以打包成exe

### 打包成jar

```sh
javafxpackager -createjar -appclass your.app.xxx -srcdir classes -outdir archive -out file Test.jar
```

### 打包成exe

```sh
javafxpackager -deploy -appclass your.app.xxx -native image -srcdir app -outdir deploy -outfile Test
```

### 说明

1. appclass 指定应用程序主类
2. srcdir 指定存放jar文件的目录
3. native image 指定输出的本地程序类型，image只生成一个启动器
4. 用该方法打包maven构建的项目，依赖的很多jar包都不会被打包进来

## javafx-maven-plugin

> com.zenjava.javafx-maven-plugin
>
> 实践版本 Java 1.8.0_251 ；Maven 3.6.3

### 版本要求

- Maven 3.5 (older versions might work too) 
- Java Developer Kit 8 with at least Update 40 (does **NOT** support JKD9 or later yet)

### 配置说明

```xml
<plugin>
    <groupId>com.zenjava</groupId>
    <artifactId>javafx-maven-plugin</artifactId>
    <version>8.8.3</version>
    <configuration>
        <!--指定打包出来的应用名-->
        <appName>appName</appName>
        <!--应用程序的供应商，在Bundles安装时候需要-->
        <vendor>yui</vendor>
        <!--入口类-->
        <mainClass>your.app.MainClass</mainClass>
        <!--JAVA启动参数-->
        <runJavaParameter>
            -Xms50m -Xmn1g -Xss228k -XX:+UseParallelGC -XX:ParallelGCThreads=20
        </runJavaParameter>
        <!--APP启动参数 For MavenPlugins jfx:run-->
        <runAppParameter>--spring.profiles.active=prod --file.encoding=UTF-8
        </runAppParameter>
        <!--native启动参数 For MavenPlugins jfx:native-->
        <launcherArguments>--spring.profiles.active=prod --file.encoding=UTF-8
        </launcherArguments>
        <!--默认目录${project.basedir}/src/main/deploy-->
        <deployDir>${project.basedir}/src/main/deploy</deployDir>
    </configuration>
</plugin>
```



### 命令说明

#### jfx:jar

1. 代码打包成jar包，依赖打包到目录lib下
2. 正常启动 (**java - jar xxx.jar**)，会报错缺少依赖文件
3. 解决方案（个人看法，没找到解决方案）
   1. 将所有依赖加入启动参数，使用start.bat启动
   2. 将所有依赖和代码打成一个包 jar-with-all-dependencies.jar

#### jfx:native[采用方案]

1. **jfx:build-native** : 只重新构建native(target/jfx/native)，需要已构建jar (target/jfx/app)
2. **jfx:native**: 重新构建jar和native

3. 个人看法：流程是生成调用jar包的exe，运行exe启动程序。


##### 如何修改程序图标？

图标所在目录：**src/main/deploy/package/windows/your-app-name.ico** （与deployDir相关）

#### jfx:run

 com.zenjava:javafx-maven-plugin:8.8.3:run 运行调试

#### jfx:web

需要**jnlp**

#### jfx:keystore

> There's nothing yet.





[Github]: https://github.com/javafx-maven-plugin/javafx-maven-plugin
[轻松生成配置项：]: https://zenjava.net/javafx-maven-plugin/



## maven-assembly-plugin

生成jar包两种方式

1. 打包项目依赖的所有jar生成单个jar包
2. 生成jar包，复制所需的jar包到lib目录

第一种方式较好，第二种方式会出现 **Caused by: java.lang.NoClassDefFoundError**错误



```xml
<plugin>
    <artifactId>maven-assembly-plugin</artifactId>
    <configuration>
        <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
        </descriptorRefs>
        <archive>
            <manifest>
                <mainClass>your.app.xxx</mainClass>
            </manifest>
        </archive>
    </configuration>
    <executions>
        <execution>
            <id>make-assembly</id>
            <phase>package</phase>
            <goals>
                <goal>single</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```



## exe4j

[参照链接：JAVAFX打包JAR成EXE](https://www.freesion.com/article/9830721030/)

## idea

![img](20190211142608269.png)

![img](20190211142748443.png)

![img](20190211142829884.png)

![img](20190211142908165.png)

## 其他

1. [GraalVM](https://blog.csdn.net/wangpaiblog/article/details/122850438)
   1. [gluonfx官网](https://docs.gluonhq.com/#_resourceslist)
2. [jdk17 + openjfx](https://www.cnblogs.com/xl4ng/p/17674096.html) 