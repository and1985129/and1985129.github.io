---
layout: post
title: 个人电脑上安装Hybris
category: Hybris
tags: Hybris
description: 个人电脑上安装Hybris
---

## 作者简介
科科香，程序员

方向：SAP，IoT，方案集成，喜好各种新鲜东东

SAP Labs工程师

**转载请注明出处**

## Hybris介绍

![image](http://7xsqph.com2.z0.glb.clouddn.com/hybris1.jpg) ![image](http://7xsqph.com2.z0.glb.clouddn.com/hybris2.jpg)

blablabla

## 安装
关于Hybris的安装，请直接到[Trail ~ Preparation](https://wiki.hybris.com/pages/viewpage.action?pageId=294094002)要按照上面的步骤一步一步的执行就行。整个步骤和描述非常清楚，没有什么意外情况都会安装成功的。这里主要介绍安装准备和安装过程中我遇到的一些问题的解决办法。

#### 注册账号
Hybris的[wiki](https://wiki.hybris.com)需要注册账号才能浏览，所以需要先注册账号，才能下载安装包。

##### 电脑配置
要安装Hybris到本机，电脑的内存配置至少为8G，少于8G的亲们还是向老板或者自己掏腰包升级一下电脑吧。

#### 操作系统
主要就是Mac和Windows了。我用的Mac，全程安装没有出过什么问题，非常顺利。周围有用Windows的童鞋在安装过程中读取配置文件build.xml以及其他文件出错，这里主要原因是Windows的路径是用“\”区分，而配置文件中是“/”，所以如果遇到此类问题，请直接将配置文件的分隔符替换成Windows中默认的就行。如果你是Windows电脑但没有遇到这个问题，就直接忽略即可。

#### 配置文件设置
因为个人电脑的性能问题，在启动Hybris服务的时候会直接死掉，这是因为配置文件中将Tomcat的内存设置太高，你需要到`$YOURPATH/hybris/bin/platform`文件夹中，修改文件`project.properties`，在第`156`行将tomcat.generaloptions的内存设为1G。

```
tomcat.generaloptions=-Xmx1G -ea ...
```
#### ANT
ANT是Hybris编译生成工具，在下载包里面已经附带了，目录是`/hybris/platform/apache-ant-1.9.1`。只需要照着wiki就可以设置ANT的环境变量。撸主是用的自己下载的ANT，版本1.9.3，貌似也米有遇到什么问题。

#### 停止服务器
*注意！！！！* 

只需要按一次CTRL+C，服务器会自动启动一些脚本来停止整个服务，如果悲催的按了超过1+次，那代表你把结束其他服务进程的脚本也给结束了，只能重启电脑来启动服务器了。

## 安装完成
好了，正常的话，安装就会成功了。总的来说，Hybris的wiki解释的非常清楚明白，所以整个安装也是非常的顺利的，接下来就可以开始你的Hybris之旅啦：）