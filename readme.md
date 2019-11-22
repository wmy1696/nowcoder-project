# nowcoder社区项目

本项目是一个基于SpringBoot的社区平台，实现了牛客网讨论区的功能。实现了邮箱注册、验证码登录、发帖、评论、私信、点赞、关注、统计网站访问次数等功能，数据库使用Mybatis、Redis，使用Kafka构建系统通知，使用Elasticsearch构建全文搜索功能。同时实现生成pdf文件、上传云服务器等功能，最后将项目部署到了实验室服务器上（ubuntu系统）。具体可以参见项目总结和项目笔记。

## 项目笔记

[1.初识Spring Boot，开发社区首页](./note/第一章.md) 

[2.Spring Boot实践，开发社区登录模块](./note/第二章.md)

[3.Spring Boot实践，开发社区核心功能](./note/第三章.md) 

[4.Redis，一站式高性能存储方案](./note/第四章.md) 

[5.Kafka，构建TB级异步消息系统](./note/第五章.md)

[6.Elasticsearch，分布式搜索引擎](./note/第六章.md) 

[7.项目进阶，构建安全高效的企业服务](./note/第七章.md) 

[8.项目发布与总结](./note/第八章.md)

## 项目总结

* Spring Boot
* **Spring**
* Spring MVC、Spring Mybatis、**Spring Security**
* 权限@会话管理
  * 注册、登录、退出、状态、设置、授权
  * Spring Email、**Interceptor**
* 核心@敏感词、@事务
  * 首页、帖子、评论、私信、异常、日志
  * Advice、**AOP**、**Transaction**
* 性能@数据结构
  * 点赞、关注、统计、缓存
  * **Redis**
* 通知@模式
  * 系统通知
  * Kafka
* 搜索@索引
  * 全文搜索
  * Elasticsearch
* 其他@线程池、@缓存
  * 排行、上传、服务器缓存
  * Quartz、**Caffeine**

## 其他

本项目是我依据牛客网2019年的高级项目，花了一个月左右完成，可以边写边学技术栈。有几点提一下吧：

1. 要做好项目笔记，方便以后回顾复习。
2. 项目涉及到的知识点要花时间去学习。
3. 每完成一个功能一定要测试，不然后面很难找到bug。
4. 有些报错重新编译一下就能解决。
5. 注意使用的库、插件、软件、JDK的版本，这个报错真心难找。
6. 部署到Linux服务器，也要注意软件版本，和windows保持一致。
7. 加油，找个好工作！

