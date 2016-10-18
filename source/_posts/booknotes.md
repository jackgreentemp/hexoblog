---
title: booknotes
date: 2016-09-06 22:24:16
tags: notes node
---
# node读书笔记

## 《Node.js项目实践 构建可扩展的Web应用》

源码：https://github.com/azat-co/practicalnode

简介：书中使用了Express4.0，介绍了：
Express脚手架、
Mocha测试驱动开发、
Mocha行为驱动开发、
Jade和Handlebars模板引擎（书中的实例主要使用Jade）、
Mongoskin（书中大部分示例使用的是Mongoskin）、
基于session的OAuth用户认证和授权、
Mongoose（使用Mongoose对博客示例进行了重构）、
基于Express.js和Hapi构建REST API服务、
使用WebSocket、Socket.IO和DerbyJS搭建实时的web应用程序，实例是一个实时更新的在线编辑器、
Node应用上线和部署

示例：博客，使用了Bootstrap，完成了文章列表、文章详情、文章发布、用户权限以及第三方登录授权（Twitter）

心得：介绍的比较全面，开发时可以参考：如何基于Express脚手架新建工程、测试驱动开发、MongoDB持久化、API服务构建以及用户认证和授权

## 《Node与Express开发》

基础内容较多，但是后面几章介绍了安全、生产环境配置、静态内容如何部署、服务托管、维护等内容，值得一读

主要使用Handlebars模板

重点关注书中表单处理、发送邮件以及生产环境配置、部署、维护等章节

## 《Node应用程序构建》

源码：https://github.com/Swiftam/book-node-mongodb-backbone

偏向与实战，以开发一个社交网站的实例介绍了Express、Mongoose、Socket.IO的使用，使用了Jade和Backbone

缺点是使用Express3.x，比较旧了，适合入门阅读

使用Mongoose的代码值得参考

实例：可以在线聊天的社交网站，使用了Socket.io完成实时操作