---
layout: post
title: Mac OSX下配置和安装Maven
category: 工具
tags: 工具
description: Mac OSX下配置和安装Maven
---

## 作者简介
科科香，程序员

方向：IoT，方案集成，喜好各种新鲜东东

**转载请注明出处**

## 安装Maven

- 安装Maven之前，需要安装好Java JDK
- 下载最新的Maven文件并解压到本地：[https://maven.apache.org/download.cgi](https://maven.apache.org/download.cgi)，记住对应的目录，例如/Users/and1985129/apache-maven-3.3.9
- 打开终端，输入一下命令，设置Maven环境变量

  ```
  $ vim ~/.bash_profile
  ```
  添加如下两行，完成后退出保存
  
  ```
  export M2_HOME=/Users/and1985129/apache-maven-3.3.9
  export M2=$M2_HOME/bin
  export PATH=$M2:$PATH
  ```
- 输入命令使.bash_profile生效

  ```
  $ source ~/.bash_profile
  ```
  
- 完成之后，在终端输入`mvn -v`查看Maven是否安装成功。如果成功，会得到如下信息

  ```
Apache Maven 3.3.9 (bb52d8502b132ec0a5a3f4c09453c07478323dc5; 2015-11-11T00:41:47+08:00)
Maven home: /Users/and1985129/apache-maven-3.3.9
Java version: 1.8.0_71, vendor: Oracle Corporation
Java home: /Library/Java/JavaVirtualMachines/jdk1.8.0_71.jdk/Contents/Home/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "mac os x", version: "10.11.2", arch: "x86_64", family: "mac"
  ```
- 异常：如果遇到异常，很有可能是没有设置JAVA_HOME，打开.bash_profile文件，添加JAVA_HOME变量`export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_71.jdk/Contents/Home`并重新运行`$ source ~/.bash_profile`即可

