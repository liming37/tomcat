## 编译tomcat源码手册

### 1 从github上拉取源码
- 登陆github网站，搜索apache/tomcat;
- 将该项目fork至自己的账号，以便创建分支，在debug过程中修改提交；
- clone 代码至自己电脑
- 从一个自己想研究的分支（如8.5.x） checkout 一个新的分支，如我新建的分支是debug-code
### 2 整理目录
1. 在根目录中新建catalina-home目录和pom.xml文件，pom.xml文件内容如下：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>
    <groupId>org.apache.tomcat</groupId>
    <artifactId>Tomcat8.0</artifactId>
    <name>Tomcat8.0</name>
    <version>8.0</version>

    <build>
        <finalName>Tomcat8.0</finalName>
        <sourceDirectory>java</sourceDirectory>
        <testSourceDirectory>test</testSourceDirectory>
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
                <version>2.3</version>
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
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.easymock</groupId>
            <artifactId>easymock</artifactId>
            <version>3.4</version>
        </dependency>
        <dependency>
            <groupId>ant</groupId>
            <artifactId>ant</artifactId>
            <version>1.7.0</version>
        </dependency>
        <dependency>
            <groupId>wsdl4j</groupId>
            <artifactId>wsdl4j</artifactId>
            <version>1.6.2</version>
        </dependency>
        <dependency>
            <groupId>javax.xml</groupId>
            <artifactId>jaxrpc</artifactId>
            <version>1.1</version>
        </dependency>
        <dependency>
            <groupId>org.eclipse.jdt.core.compiler</groupId>
            <artifactId>ecj</artifactId>
            <version>4.5.1</version>
        </dependency>

    </dependencies>
</project>
```
2. 将根目录中的conf和webapps文件夹移动到catalina-home目录中，并删除webapps内的example目录
3. 在catalina-home目录中新建四个空目录：lib、temp、work、logs
### 3 配置IDEA
1. 在idea中导入源码(选择导入项目)
2. 在Run/Debug Configurations中新建"Application"，设置Main class为org.apache.catalina.startup.Bootstrap，设置VM options为：
```
-Dcatalina.home="/Users/llming/Documents/study/code/tomcat/catalina-home"
-Duser.language=en
-Duser.region=US
-Dfile.encoding=UTF-8
```
注意每个idea版本设置不一样，有的idea版本新增"Applicatin"时，界面中的VM option被隐藏了，需要手动调出来，如点击界面中的`Modify options`

### 4 启动
1. 启动刚配置好的启动Run/Debug Configurations
2. 在启动过程中会遇到某些测试类加载不到类，直接把他们给注释掉（没有几个，多注释几次就好）
3. 启动后在浏览器访问：http://localhost:8080，如果报500，则在源码`org.apache.catalina.startup.ContextConfig`这个类的第779行（8.5.x分支）也就是`webConfig();`后添加以下这行：
```c++
context.addServletContainerInitializer(new JasperInitializer(), null);
```
原因是我们直接启动org.apache.catalina.startup.Bootstrap的时候没有加载org.apache.jasper.servlet.JasperInitializer，从而无法编译JSP。解决办法是在tomcat的源码org.apache.catalina.startup.ContextConfig中手动将JSP解析器初始化;
4. 到此便可以正常启动


