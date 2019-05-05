---
layout: post
title: "RESTful API 的原理和使用"
date: 2019-05-05
excerpt: "参考 wiki 可知 REST 是 Representational State Transfer 表现层状态转换的英文缩写，由 Roy Thomas Fielding于 2000 年提出，是基于 HTTP 之上而确定的一组约束和属性，是一种设计提供万维网络服务的软件构建风格。"
tags: [API, basic]
comments: true
---

> 参考文档：  
> [Vinay Sahni（英原） | Best Practices for Designing a Pragmatic RESTful API](https://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api)  
> [bruce-accumulate（中译） | RESTful API 设计最佳实践](http://blog.jobbole.com/41233/)  
> [阮一峰 | RESTful API 设计指南](http://www.ruanyifeng.com/blog/2014/05/restful_api.html)  
> [维基百科 | 表现层状态转换](https://zh.wikipedia.org/wiki/表现层状态转换)  
> [Roy T. Fielding | Architectural Styles and
the Design of Network-based Software Architectures, CHAPTER 5, Representational State Transfer (REST) ](https://www.ics.uci.edu/~fielding/pubs/dissertation/rest_arch_style.htm)  

---

在我看来 Vinay Sahni 的文章已经很简单易懂了，以下是针对他的文章对自己的知识点梳理和补充，速成建议直接查看第三条参考文档阮一峰的文章。

### 什么是 REST
开始先粗略了解一下 REST。参考 wiki 可知 REST 是 Representational State Transfer 表现层状态转换的英文缩写，由 [Roy Thomas Fielding](https://www.ics.uci.edu/~fielding/) 于 2000 年提出，是***基于 HTTP*  之上而确定的一组约束和属性，是一种设计提供万维网络服务的 *软件构建风格* ，目的是便于不同软件/程序在网络（例如互联网）中互相传递信息**  。要注意的是这并不是 API 编写标准，而是一种广为接受的设计风格。它的特点在于

> 1. **Client-Server 客户端-服务器结构**
将浏览器视为客户端，使用客户端服务器分离模式，任何一个客户端与服务器都是可替换的；只能由客户端向服务器端发起请求，服务器处理并返回结果；支持跨平台跨语言。
> 2. **Stateless 无状态**
对同一个url请求没有上下文关系，每次的请求都是独立的，服务器中没有保存客户端的状态，通信的会话状态全部由客户端保存维护。
> 3. **Cache 缓存**
响应内容可以在通信链的某处被缓存，以改善网络效率。缓存约束要求响应请求的数据中需明确标识是否可被缓存。如果响应信息是可缓存的，那么客户端将被赋予权限以用于重复请求。
> 4. **Uniform Interface 统一接口**
通信链的组件之间通过统一的接口相互通信，以提高交互的可见性。
> 5. **Layered System 分层系统**
通过限制组件的行为（即每个组件只能“看到”与其交互的紧邻层），将架构分解为若干等级的层。
> 6. **Code-On-Demand (Optional) 按需代码（可选）**
支持通过下载并执行一些代码（例如Java Applet、Flash或JavaScript），对客户端的功能进行扩展。

在 REST 中，resource 资源的定义在于
> Any information that can be named can be a resource: a document or image, a temporal service (e.g. "today's weather in Los Angeles"), a collection of other resources, a non-virtual object (e.g. a person), and so on. In other words, any concept that might be the target of an author's hypertext reference must fit within the definition of a resource. A resource is a conceptual mapping to a set of entities, not the entity that corresponds to the mapping at any particular point in time.
> 任何信息都可以视作一个资源：一份文档或者一张图片，一个 temporal service（例如：洛杉矶今天的天气），其他

### 什么是 RESTful API
那么 RESTful API 指的是什么呢：
> The key principles of REST involve separating your API into logical resources. These resources are manipulated using HTTP requests where the method (GET, POST, PUT, PATCH, DELETE) has specific meaning.
> REST 的核心原则是将你的 API 拆分为逻辑上的资源。这些资源通过http被操作（GET ,POST,PUT,DELETE）。

那么对于符合以下 REST 设计风格的 Web API 称为 RESTful API：

>  1. 直观简短的 URI 指定资源地址；
>  2. 传输的资源的表现形式是Web服务接受与返回的互联网媒体类型，比如：JSON，XML，YAML等；
>  3. 使用 HTTP 协议对资源进行操作。

>    扩展阅读：
> * [ Dr. M. Elkstein |
Learn REST: A Tutorial](http://rest.elkstein.org/2008/02/what-is-rest.html)
> * [stackoverflow | Best practices for API versioning?](https://stackoverflow.com/questions/389169/best-practices-for-API-versioning)
> * [赛艇队长 | http协议无状态中的 "状态" 到底指的是什么？！](https://www.cnblogs.com/bellkosmos/p/5237146.html)
