+++
title = "关于开发者测试与测试框架"
author = ["alilili"]
lastmod = 2023-09-18T00:27:16+08:00
draft = false
+++

## 什么是一个好的测试 {#什么是一个好的测试}

1.  ****应该是独立可重复的****. 每一个测试的环节应该是独立而且是可解耦的. 一个环节没跑通不应该影响后续环节. 一个环节的错误也不应该向后传, 一个环节不符合预期应该立刻报出来. 一个环节出了问题, 可以反复测试这个环节而不用再把其他环节再跑一遍.
2.  ****测试代码本身应该有良好的结构****. 不仅代码要有良好的结构, 测试代码也要有良好的结构. 相同的测试环节之间应该构成一个模块. 代码应该是平台无关的, 代码应该是可以复用的.
3.  ****打印尽可能多的信息****. 测试中不需要考虑打印信息太多, 造成阅读困难这一个问题. 而且测试的目的本来就是希望再测试的过程中发现尽可能多的问题.


## xUnit {#xunit}


## CUnit {#cunit}


### 介绍 {#介绍}

1.  继Junit CppUnit的成功后， c语言环境下也出现了开放源码的白盒测试用例CUnit。CUnit以静态库的形式提供给用户使用，用户编写程序的时候直接链接此静态库就可以了。它提供了一个简单的单元测试框架，并且为常用的数据类型提供了丰富的断言语句支持。
2.  静态测试，方便操作。
3.  CUnit可以结合gcov lcov等使用
4.  可以看出Cunit也是有组织的，呵呵，主要分几个角色，Registry，Suite及Test方法。可以通过下面例子，体会到这种组织关系。

按官方文档说明，使用Cunit的主要步骤有：

1.  Write functions for tests (and suite init/cleanup if necessary).
2.  Initialize the test registry - `CU_initialize_registry()`
3.  Add suites to the test registry - `CU_add_suite()`
4.  Add tests to the suites - `CU_add_test()`
5.  Run tests using an appropriate interface, e.g. `CU_console_run_tests`
6.  Cleanup the test registry - `CU_cleanup_registry`


### 获取 {#获取}

[CUnit网址](http://cunit.sourceforge.net/)


## google test {#google-test}


### 获取 {#获取}

googlttest github 地址： [googletest github](https://github.com/google/googletest)


### 简介 {#简介}

是一个跨平台的C/C++单元测试框架. 包括GoogleTest和GoogleMock两个部分.

关于单元测试具体可以参考:
[xUnit](https://en.wikipedia.org/wiki/XUnit)

特性:

-   丰富断言接口
-   自定义断言接口
-   死亡测试
-   致命和非致命测试
-   值-参数化测试
-   类型-参数化测试
-   运行测试的各种选项
-   导出XML测试报告


### GoogleMock {#googlemock}

是一个GoogleMock用来生产模拟(Mock)类的框架.

主要包括一下功能:

-   使用一些简单的宏描述你想要模拟的接口，他们将扩展到你的mock类的实现;
-   创建一些模拟对象，并使用直观的语法指定其期望和行为;
-   练习使用模拟对象的代码。 Google Mock会在出现任何违反期望的情况时立即处理。


### GTest Runner {#gtest-runner}


#### 简介 {#简介}

是一个基于Qt5的自动化运行测试和图形化用户界面. 支持Windows和Linux平台.


### GoogleTest UI {#googletest-ui}


#### 简介 {#简介}

图形化运行二进制文件的框架.


### GTest TAP Listener {#gtest-tap-listener}


#### 简介 {#简介}

监听GoogleTest事件, 来充填TAP来实现测试结果输出.

[TAP protocol](https://en.wikipedia.org/wiki/Test_Anything_Protocol)介绍.


### gtest parellel {#gtest-parellel}


#### 简介 {#简介}

并行运行程序实现, 二进制文件测试加速.


### GoogleTest Adapter {#googletest-adapter}


#### 简介 {#简介}

是一个VS Code可视化GoogleTest的VS Code插件.


## catch2 {#catch2}
