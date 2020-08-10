---
layout: post
title:  "Vscode+Maven（记录最小化的搭建环境步骤）"
date: 2020-08-10 13:31:23 +0800
description: Vscode+Maven（记录最小化的搭建环境步骤）
categories: blog
layout: post
typora-root-url: ../../virenz.github.io
---

<!-- ![]({{ '/assets/img/20200810133123_1.jpg' | prepend: site.baseurl }}){:height='80%' width='80%'} -->



### 1. VsCode

> VsCode越来越强大，有时候简单修改并编译Java工程使用小巧的VsCode反而更加方便快捷，萌生了搭建一套VsCode+Maven开发环境



### 2. Maven

> [Maven](https://maven.apache.org/)是一个软件工具，可帮助您管理Java项目和自动化应用程序构建。用于Visual Studio Code 的[Maven for Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-maven)扩展提供了完全集成的Maven支持，使您可以浏览Maven项目，执行Maven命令以及执行构建生命周期和插件的目标。
>
> 插件名：Maven for Java
>
> Java配置就不描述了（自行google吧）

​	 ![配置Maven地址](/assets/img/2020-08-10-2.38.26.png)



### 3. 编写pom.xm

- 基本配置信息

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  
      <modelVersion>4.0.0</modelVersion>
      <!-- 
          含义：组织标识，定义了项目属于哪个组，风向标，坐标，或者说若把本项目打包
          用途：此名称则是本地仓库中的路径，列如：by.wr，在M2_REPO目录下，将是: by/wr
          命名规范:项目名称，模块，子模块
      -->
      <groupId>by.wr</groupId>
      <!-- 
          含义：项目名称也可以说你所模块名称，定义当面Maven项目在组中唯一的ID
          用途：例如：demo，在M2_REPO目录下，将是：by/wr/demo目录
          命名规范:唯一就好
      -->
      <artifactId>demo</artifactId>
      <!-- 
          含义：项目当前的版本号
          用途：例如：0.0.1-SNAPSHOT
      -->
      <version>1.0.0-SNAPSHOT</version>
      <!-- 打包的格式，可以为：pom , jar , maven-plugin , ejb , war , ear , rar , par -->
      <packaging>jar</packaging>
      <!-- 元素声明了一个对用户更为友好的项目名称 -->
      <name>${project.artifactId}-${project.version}</name>
  
      <url>http://maven.apache.org</url>
  
  	<properties>
  		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>
  ```

- 添加依赖jar

  ```xml
  <!-- 添加maven库内的依赖jar 可直接打包进主执行jar -->
  <dependencies>
    <dependency>  
      <groupId>org.objectweb</groupId>
      <artifactId>asm</artifactId>
      <version>7.0</version>
    </dependency>
  </dependencies>
  
  
  <!-- 添加本地的依赖jar 不可直接打包进主执行jar -->
  <dependencies>
    <dependency>  
      <groupId>org.objectweb</groupId>
      <artifactId>asm</artifactId>
      <version>7</version>
      <scope>system</scope>  
      <systemPath>${project.basedir}/libs/htmlunit-2.21-OSGi.jar</systemPath>  
    </dependency>
  </dependencies>
  ```

  

- build打包

  ```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.1</version>
        <configuration>
          <source>1.8</source>
          <target>1.8</target>
        </configuration>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-resources-plugin</artifactId>
        <version>2.6</version>
        <configuration>
          <encoding>UTF-8</encoding>
        </configuration>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.1.0</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <transformers>
                <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                  <mainClass>by.wr.test.Main</mainClass>
                </transformer>
              </transformers>
              <finalName>test</finalName>
              <outputDirectory>
                ${project.basedir}
              </outputDirectory>
            </configuration>
          </execution>
        </executions>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-dependency-plugin</artifactId>
        <executions>
          <execution>
            <id>copy</id>
            <phase>package</phase>
            <goals>
              <goal>copy-dependencies</goal>
            </goals>
            <configuration>
              <outputDirectory>${project.build.directory}/lib</outputDirectory>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
  ```



### 4. 解决的问题

1. 本地依赖jar如果要打包进主执行jar, 实验得出比较好用的方式直接mvn install本地.m2库内，然后dependency
   `mvn install:install-file -Dfile=asm-v7.jar -DgroupId=org.objectweb -DartifactId=asm -Dversion=7 -Dpackaging=jar`

2. mvn配置路径

   `可以单独下载maven，也可以使用IDEA内置的，设置文件权限即可`

