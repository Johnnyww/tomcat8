# tomcat8
已经配置好的的tomcat8源码调试项目

配置过程如下：
使用的JDK版本为：OracleJDK1.8

## 1.下载[Tomcat源码](https://tomcat.apache.org/download-80.cgi)
找到Source Code Distributions下的链接进行下载。

## 2.配置POM文件
下载下来解压以后，因为是Tomcat8.5的话应该会得到一个apache-tomcat-8.5.53-src的文件夹，里面就是Tomcat的源码了，此时我们要将其运行起来，我们采用了Maven配置的方式，配置Pom文件下载Tomcat运行需要的jar包。
新建一个文件夹，例如我建立的是tomcat8这个文件夹，然后将源码解压下来的文件夹放入其中，并且在tomcat8文件夹下建立pom.xml文件。此时的目录结构是这样的。
```
-- tomcat8
	-- apache-tomcat-8.5.53-src
	-- pom.xml
```

这个pom.xml文件的内容如下：
```xml
<?xml version="1.0" encoding="UTF-8"?>    
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"    
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">    
    
    <modelVersion>4.0.0</modelVersion>    
    <groupId>com.chenjunxin</groupId>    
    <artifactId>apache-tomcat-8</artifactId>    
    <name>apache-tomcat-8-source</name>    
    <version>1.0</version>
    <packaging>pom</packaging>    
    
    <modules>    
        <module>apache-tomcat-8.5.53-src</module>    
    </modules>    
</project>
```
然后在apache-tomcat-8.5.53-src文件夹下面也建立一个pom.xml文件，此时目录结构如下：
```
-- tomcat8
	-- apache-tomcat-8.5.53-src
		-- pom.xml
	-- pom.xml
```
此pom.xml文件内容如下:
```xml
<?xml version="1.0" encoding="UTF-8"?>    
<project xmlns="http://maven.apache.org/POM/4.0.0"    
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"    
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">    
    
    
    <modelVersion>4.0.0</modelVersion>    
    <groupId>org.apache.tomcat</groupId>    
    <artifactId>Tomcat8.5</artifactId>    
    <name>Tomcat8.5</name>    
    <version>8.5</version>    
    
    <build>    
        <finalName>Tomcat8.5</finalName>    
        <sourceDirectory>java</sourceDirectory>    
<!--        <testSourceDirectory>test</testSourceDirectory>    -->
        <resources>    
            <resource>    
                <directory>java</directory>    
            </resource>    
        </resources>    
        <testResources>    
            <testResource>    
                <directory>test</directory>    
            </testResource>    
        </testResources>    
        <plugins>    
            <plugin>    
                <groupId>org.apache.maven.plugins</groupId>    
                <artifactId>maven-compiler-plugin</artifactId>    
                <version>2.0.2</version>    
    
                <configuration>    
                    <encoding>UTF-8</encoding>    
                    <source>1.8</source>    
                    <target>1.8</target>    
                </configuration>    
            </plugin>    
        </plugins>    
    </build>

    <dependencies>
        <dependency>
            <groupId>ant</groupId>
            <artifactId>ant</artifactId>
            <version>1.7.0</version>
        </dependency>
        <dependency>
            <groupId>ant</groupId>
            <artifactId>ant-apache-log4j</artifactId>
            <version>1.6.5</version>
        </dependency>
        <dependency>
            <groupId>ant</groupId>
            <artifactId>ant-commons-logging</artifactId>
            <version>1.6.5</version>
        </dependency>
        <dependency>
            <groupId>wsdl4j</groupId>
            <artifactId>wsdl4j</artifactId>
            <version>1.6.2</version>
        </dependency>
        <dependency>
            <groupId>javax.xml.rpc</groupId>
            <artifactId>javax.xml.rpc-api</artifactId>
            <version>1.1</version>
        </dependency>
        <dependency>
            <groupId>org.eclipse.jdt.core.compiler</groupId>
            <artifactId>ecj</artifactId>
            <version>4.6.1</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

## 3.配置启动参数
配置完pom.xml文件以后就可以配置启动参数了，此时用编译器打开此项目。我用的是IDEA，在右上角点击配置，然后添加一个Application
![](https://raw.githubusercontent.com/Johnnyww/tomcat8/master/pictures/tomcat8_add_application.webp)

此时需要配置三个参数：
![](https://raw.githubusercontent.com/Johnnyww/tomcat8/master/pictures/tomcat8_debug_settings.webp)
- Main class : 固定填写org.apache.catalina.startup.Bootstrap即可，表示Tomcat的启动类是哪个
- VM options : -Dcatalina.home="war包存放路径"，例如我是在源码路径下建立了一个home文件夹，home文件里有conf、webapps、work、logs、lib、文件夹。这里面其实就是Tomcat的工作路径了
- Use classpath of module : 设置为新建文件夹的模块

## 4.问题解决
### 控制台部分中文日志乱码问题
编译完成后遇到的两个问题：1.部分本地化的中文日志乱码。2.官方测试页面跳转不正常。
参考链接:https://blog.csdn.net/qq_40169542/article/details/104351826 进行配置解决。
1. 把正常部署tomcat的lib目录下的tomcat-i18n-zh-CN.jar解压然后把复制所有的本地化文件直接在源码的java目录下替换就是了，他们的目录结构是一致的。官方编译应该是有工具和脚本进行转义处理的。(Windows系统下直接复制过去会出现替换选项)。这样之后再次启动编译好的tomcat你就会发现输出日志正常了。
2. 复制正常部署在webapp目录下官方项目替换掉编译部署的webapp中的项目。解决tomcat启动官方首页正常，但是点击或者是访问其他资源报错。

### JSP访问报错问题
访问JSP的时候却报错了，比如访问：http://localhost:8080/index.jsp 或者我们自己的项目的时候会出现JSP无法编译的错误。
原因是我们直接启动org.apache.catalina.startup.Bootstrap的时候没有加载org.apache.jasper.servlet.JasperInitializer，从而无法编译JSP。这在Tomcat6/7是没有这个问题的。解决办法是在tomcat的源码org.apache.catalina.startup.ContextConfig中手动将JSP解析器初始化：
```java
context.addServletContainerInitializer(new JasperInitializer(), null);
```
![](https://raw.githubusercontent.com/Johnnyww/tomcat8/master/pictures/tomcat8_jsp_bug_fix.webp)

## 参考链接
- [基于IntelliJIDEA环境Tomcat8源码的调试和项目部署](https://gongxufan.github.io/2017/10/20/tomcat-source-debug/)
- [如何断点调试Tomcat源码](https://juejin.im/post/5cf6366ce51d45105e021275)
