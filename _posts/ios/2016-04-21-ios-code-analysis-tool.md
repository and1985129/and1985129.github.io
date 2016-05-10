---
layout: post
title: iOS代码分析检查工具
category: iOS
tags: iOS
description: iOS代码分析检查工具
---

## 作者简介
科科香，程序员

方向：SAP，IoT，方案集成，喜好各种新鲜东东

SAP Labs工程师

**转载请注明出处**

最近为公司的新产品做了一个iOS版本的demo示例，中间因为和其他开发人员有共同协作开发，等到项目收尾的时候一看代码，才发现代码写的乱七八糟的，于是想找找有没有什么iOS开发的代码检查分析工具，可以让代码更加规范健壮且易懂。经过度娘+谷哥混合搭配，找到如下系列工具。

## 本地自动分析工具

### Performing Static Code Analysis
Xcode自带的代码分析工具。在菜单栏：Product-> Analysis（Command+Shift+B）。

### SwiftLint
[SwiftLint](https://github.com/realm/SwiftLint)，只做Swift的代码检查分析。

可以通过命令行执行或集成到Xcode中。

### Tailor
[Tailor](https://tailor.sh)也只是做Swift的代码检查分析。

除外，他可以在Linux或者Windows平台上做跨平台的代码分析。

## Jenkins自动分析工具
一般企业级的开发，iOS代码都是通过Jenkins自动部署，所以通过Jenkins来做代码分析检查，会大大提高代码的产出。

[Clang Scan-Build Plugin](https://github.com/jenkinsci/clang-scanbuild-plugin)就是这样一个利器。


## 人肉检查分析清单
再好的自动化代码检视工具也会有遗漏的时候，人肉就是填坑的最终武器了。

- [http://www.jianshu.com/p/71fdd1ae714c](http://www.jianshu.com/p/71fdd1ae714c)
- [http://blog.manbolo.com/2014/04/15/automated-static-code-analysis-with-xcode-5.1-and-jenkins](http://blog.manbolo.com/2014/04/15/automated-static-code-analysis-with-xcode-5.1-and-jenkins)