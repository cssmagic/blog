# [译] [PJA] 《JavaScript 应用程序设计》目录

![programming-javascript-applications-cover](https://f.cloud.github.com/assets/1231359/794047/afc032be-ec64-11e2-853b-9dc795bf263d.jpg)

## 译者按

> 这是一本 O'Reilly 的新书，于 2013 年 2 月发布 [在线预览版](http://chimera.labs.oreilly.com/books/1234000000262/index.html)，目前还未正式出版。

> 本书全名《Programming JavaScript Applications: Robust Web Architecture With Node, HTML5, and Modern JS Libraries》，暂译作《JavaScript 应用程序设计：基于 Node、HTML5 及主流 JS 类库架构稳健的 Web 应用》，本博客使用其缩写 `[PJA]` 作为代号。

> **声明**：本书版权属于 O'Reilly 公司。本人参与翻译工作仅出于学习的目的。任何组织与个人不得在未获授权的情况下将本书原文与译文用于商业用途。

> **警告**：这本书目前并非最终版本，只是作者提供的原始书稿，还未经润色与审校。因此书中可能存在错漏或不严谨的内容，请合理使用。另外，译者的专业水平和精力均十分有限，仅供参考，后果自负。

## 目录

> * 部分术语的翻译可能有误，请在本页底部评论，协助纠正。
> * 当任一章节翻译完成后，会在下面的目录中加链接。

### Preface

* Conventions Used in This Book
* Using Code Examples
* Safari&reg; Books Online
* How to Contact Us
* Introduction
* Who this Book is For
* Who this Book is Not For
* About the Author

### 序言

* 本书所使用的约定
* 使用示例代码
* Safari 图书在线
* 如何联系我们
* 导言
* 这本书适合谁
* 这本书不适合谁
* 关于作者

### Chapter 1\. The JavaScript Revolution

* Performance
* Objects
* Syntax
* First Class Functions
* Events
* Reusability
* The Net Result
* Anatomy of a Typical Modern JavaScript App
	* Infrastructure
	* JSON: Data Storage and Communication
	* NoSQL Datastores
	* RESTful JSON Web Services

### 第一章 JavaScript 革命

* 性能
* 对象
* 语法
* 一等公民——函数
* 事件
* 重用
* 最终结果
* 解构一个典型的现代 JavaScript 应用
	* 底层架构
	* JSON：数据的存储与通信
	* NoSQL 数据库存储
	* REST 风格的 JSON 网页接口

### Chapter 2\. JavaScript Style Guide

* Example Tests
	* QUnit Primer
* Code Quality
	* Best Practices Quick Reference

### 第二章 JavaScript 代码风格指南

* 测试示例
	* QUnit 入门
* 代码质量
	* 最佳实践的快速参考

### Chapter 3\. Functions

* Function Definition
	* Named Function Expressions
	* Lambdas
	* Immediately Invoked Function Expressions
	* Method Context
* Function Scope
	* Hoisting
	* Closures
* Method Design
	* Named Parameters
	* Function Polymorphism
	* Generics and Collection Polymorphism
	* Method Chaining and Fluent APIs
* Functional Programming
	* Stateless Functions (aka Pure Functions)
	* Partial Application and Currying
* Asynchronous Operations
	* Callbacks
	* Promises and Deferreds

### 第三章 函数

* 函数定义
	* 具名函数表达式
	* Lambda 函数
	* 立即调用的函数表达式
	* 方法的上下文
* 函数作用域
	* 声明的隐式提升
	* 闭包
* 方法的设计
	* 为参数命名
	* 函数的多态特性
	* 泛型与集合多态
	* 链式方法与语流式 API
* 函数式编程
	* 无状态函数（亦称纯粹函数）
	* 偏函数应用与科里化
* 异步操作
	* 回调函数
	* 许诺机制与延迟执行

### Chapter 4\. Objects

* Classical Inheritance is Obsolete
* Fluent Style JavaScript
* Prototypes
	* The Prototype Property
	* Prototype Cloning
	* The Flyweight Pattern
* Object Creation
* Factories
* A Prototypal Object Creation Library
	* Object Creation
	* Factory Creation

### 第四章 对象

* 传统的继承已经过时了
* 语流风格的 JavaScript
* 原型
	* 原型属性
	* 复制原型
	* 享元模式
* 创建对象
* 工厂函数
* 创建原型对象的类库
	* 对象创建法
	* 工厂创建法

### Chapter 5\. Modules

* Principles of Modularity
* Interfaces
* The Module Pattern
* AMD
	* Plugins
* CommonJS Modules
* npm
	* Directives
* Harmony Modules
* Building Client-Side Code With CommonJS, npm, Grunt, and Browserify
	* Defining the App

### 第五章 模块

* 模块化的原则
* 接口
* 模块的模式
* AMD 规范
	* 插件
* CommonJS 模块规范
* npm 包管理器
	* 指令介绍
* Harmony 的模块机制
* 基于 CommonJS、npm、Grunt 和 Browserify 构建客户端代码
	* 定义你的应用

### Chapter 6\. Separation of Concerns

* Client Side Concerns
	* Module Management
	* Events
	* MVC / MV\*
	* Presentation and DOM Manipulation
* Server Side Concerns
	* Getting Started With Node and Express
* Internationalization
	* Locale Determination

### 第六章 关注点分离

* 客户端的关注点
	* 模块管理
	* 事件
	* MVC / MV\* 模式
	* 表现层与 DOM 操作
* 服务器端的关注点
	* 从 Node 和 Express 开始入手
* 国际化工项
	* 地域探测

## 关于作者

![programming-javascript-applications-author](https://f.cloud.github.com/assets/1231359/794048/b3932392-ec64-11e2-9ed5-db7327973502.jpg)

### Eric Elliott

Eric Elliott is a veteran of JavaScript application development. His roles include JavaScript Lead at Tout (social video), Senior JavaScript Rockstar at BandPage (an industry leading music app), head of client side architecture at Zumba Fitness (the leading global fitness brand), several years as a UX and viral application consultant, and author of h5Validate, an HTML5 form validation plugin for jQuery.

### 埃瑞克·埃利奥特

埃瑞克·埃利奥特一名 JavaScript 应用开发高手。他的身份数不胜数：

* Tout（一家视频分享网站）的 JavaScript 领头人
* BandPage（一款业内领先的音乐应用）上的资深 JavaScript 大腕
* Zumba Fitness（全球领先的健身品牌）的客户端架构领导者
* 多年的用户体验与病毒营销领域的顾问
* 以及 h5Validate（一款 HTML5 表单校验 jQuery 插件）的作者等等

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/16)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
