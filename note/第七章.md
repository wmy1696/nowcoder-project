# 项目进阶，构建安全高效的企业服务

## 1. Spring Security

### 介绍

* 简介
  * Spring Security是一个专注与为Java应用程序提供身份认证和授权的框架，它的强大之处在于它可以轻松扩展以满足自定义的需求。
* 特征
  * 对身份的认证和授权提供全面的、可扩展的支持。
  * 防止各种攻击，如会话固定攻击、点击劫持、csrf攻击等。
  * 支持与Servelt API、Spring MVC等Web技术集成。
* 原理
  * 底层使用Filter（javaEE标准）进行拦截
  * Filter-->DispatchServlet-->Interceptor-->Controller(后三者属于Spring MVC)
* 推荐学习网站：www.spring4all.com
  * 看几个核心的Filter源码

### 使用

* 导包：spring-boot-starter-security
* User实体类实现UserDetails接口，实现接口中各方法（账号、凭证是否可用过期，管理权限）
* UserService实现UserDetailsService接口,实现接口方法（security检查用户是否登录时用到该接口）
* 新建SecurityConfig类
  * 继承WebSecurityConfigurerAdapter
  * 配置忽略静态资源的访问
  * 实现认证的逻辑，自定义认证规则（AuthenticationManager: 认证的核心接口）
    * 登录相关配置
    * 退出相关配置
  * 委托模式: ProviderManager将认证委托给AuthenticationProvider.
  * 实现授权的逻辑
    *  授权配置
    * 增加Filter,处理验证码
    * 记住我
* 重定向，浏览器访问A,服务器返回302，建议访问B.一般不能带数据给B（Session和Cookie）
* 转发，浏览器访问A，A完成部分请求，存入Request,转发给B完成剩下请求。（有耦合）

* 在HomeController添加认证逻辑
  * 认证成功后,结果会通过SecurityContextHolder存入SecurityContext中.

## 2. 权限控制

### 登录检查

* 之前采用拦截器实现了登录检查，这是简单的权限管理方案，现在将废弃。
  * 修改WebMvcConfig，将loginRequiredInterceptor注释。

### 授权配置

* 对当前系统内的所有的请求，分配访问权限（普通用户、板主、管理员）。
  * 新建SecurityConfig类，配置静态资源都可以访问
  * 配置授权操作，以及权限不够时的处理

### 认证方案

* 绕过Security认证流程，采用系统原来的认证方案。
  * Security底层默认会拦截/logout请求,进行退出处理。覆盖它默认的逻辑,才能执行我们自己的退出代码.
  * 这里没有用Security进行认证，需要将结果自己存入SecurityContext
  * UserService增加查询用户权限方法 
  * 在LoginTicketInterceptor,构建用户认证的结果,并存入SecurityContext,以便于Security进行授权.

### CSRF配置

* 防止CSRF攻击的基本原理，以及表单、AJAX的相关配置。
  * CSRF攻击：某网站盗取你的Cookie（ticket）凭证，模拟你的身份访问服务器。（发生在提交表单的时候）
  * Security会在表单里增加一个TOCKEN(自动生成)
  * 异步请求Security无法处理，在html文件生成CSRF令牌，（异步不是通过请求体传数据，通过请求头）
  * 发送AJAX请求之前,将CSRF令牌设置到请求的消息头中.

## 3. 置顶、加精、删除

### 功能实现

* 点击“置顶”、“加精”、“删除”，修改帖子的状态
  * 在DiscussPostMapper增加修改方法
  * DiscussPostService、DiscussPostController相应增加方法，注意在Es中同步变化
  * 要在EventConsumer增加消费删帖事件
  * 修改html和js文件

### 权限管理

* 版主可以执行“置顶”、“加精”操作。管理员可以执行“删除”操作。
  * 在SecurityConfig类下配置，置顶、加精、删除的访问权限。

### 按钮显示

* 版主可以看到“置顶”、“加精”按钮。管理员可以看到“删除“按钮。
  * 导包：thymeleaf-extras-springsecurity5，thymeleaf对security的支持。

## 4. Redis高级数据类型

### HyperLoglog

* 采用一种基数算法，用于完成独立总数的统计。
* 占据空间小，无论统计多少个数据，只占12K的内存空间。
* 不精确的统计算法，标准误差为0.81%。

### Bitmap

* 不是一种独立的数据结构，实际上就是字符串。
* 支持按位存取数据，可以将其看成是byte数组。
* 适合存储大量的连续的数据的布尔值。

## 5. 网站数据统计

### UV(Unique Visitor)

* 独立访客，需通过用户IP排重新统计数据。
* 每次访问都要进行统计。
* HyperLoglog,性能好，且存储空间小。

### DAU(Daily Active User)

* 日活跃用户，需通过用户ID排重新统计数据。
* 访问过一次，则认为其为活跃。QW
* Bitmap,性能好、且可以统计精确的结果。

新建DataService类进行统计操作。表现层一分为二，首先是何时记录这个值，其次是查看。记录值在拦截器写比较合适。新建DataInterceptor和DataController。

返回时使用forward转发，表明当前请求仅完成一半，还需另外一个方法继续处理请求。

## 6. 任务执行和调度

### JDK线程池

* ExecutorService
* ScheduledExecutorService(可以执行定时任务)

### Spring 线程池

* ThreadPoolTaskExecutor
* ThreadPoolTaskScheduler（分布式环境可能出问题）

### 分布式定时任务

* Spring Quartz（将数据存储到数据库，分布式时可以共享数据）
  * 核心调度接口Scheduler
  * 定义任务的接口Job的execute方法
  * Jobdetail接口来配置Job的名字、组等
  * Trigger接口配置Job的什么时候运行、运行频率
  * QuartzConfig：配置 -> 数据库 -> 调用

* FactoryBean可简化Bean的实例化过程:
  1. 通过FactoryBean封装Bean的实例化过程
  2. 将FactoryBean装配到Spring容器里
  3. 将FactoryBean注入给其他的Bean.
  4. 该Bean得到的是FactoryBean所管理的对象实例.

## 7. 热帖排行

* Nowcoder

  * log(精华分 + 评论数 * 10 + 点赞数 * 2)+（发布时间 - 牛客纪元）
  * 在发帖、点赞、加精时计算帖子分数（存入Redis中）
  * 新建PostScoreRefreshJob类进行处理

## 8. 生成长图

* wkhtmltopdf
  * wkhtmltopdf url file
  * wkhtmltoimage url file
* java
  * Runtime.getRuntime().exec()

## 9. 将文件上传至云服务器

* 客户端上传
  * 客户端将数据提交给云服务器，并等待其响应。
  * 用户上传头像时，将表单数据提交给云服务器。

* 服务器直传
  * 应用服务器将数据直接提交给云服务器，并等待其响应。
  * 分享时，服务端将自动生成的图片，直接提交给云服务器。

## 10. 优化网站性能

* 本地缓存
  * 将数据缓存在应用服务器上，性能最好。
  * 常用缓存工具：Ehcache、Cuava、Caffeine等。
* 分布式缓存
  * 将数据缓存在NoSQL数据库上，跨服务器。
  * 常用缓存工具：MemCache、Redis等。
* 多级缓存
  * ->一级缓存（本地缓存）->二级缓存（分布式缓存）-> DB
  * 避免缓存雪崩（缓存失效，大量请求直达DB），提高系统的可用性。