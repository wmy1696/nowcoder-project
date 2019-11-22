# Elasticsearch，分布式搜索引擎

## 1. Elasticsearch入门

### Elasticsearch简介

- 一个分布式的、Restful风格（请求标准的描述）的搜索引擎。
  - 支持对各种类型的数据的检索。
  - 搜索速度快，可以提供实时的搜索服务。
  - 便于水平扩展，每秒可以处理PB级海量数据。

### Elasticsearch术语

- 索引（对应数据库）、类型（对应表）、文档（表里一行）、字段（一列）。   最新的版本类型被废弃。                                                                                                                                                                                                                                                                                                                                                                                                                               
- 集群(服务器组合在一起)、节点（集群中每台服务器）、分片（对索引的划分）、副本（分片的备份）。

通过ES搜索的数据必须要在ES中转存一份，某种角度来说它是一个数据库。

### Elasticsearch使用

* 安装、修改配置文件
  * elasticsearch.yml文件，修改cluster.name，path.data，path.logs
  * 配置环境变量
* 安装中文分词插件（ES仅支持中文分词）
  * ik插件安装到plugins文件夹下
* 安装postman(提交html数据给ES)模拟web客户端
* 启动ES:打开bin/elasticsearch.bat
  * 查看集群健康状态：curl -X GET "localhost:9200/_cat/health?v"
  * 查看节点：curl -X GET "localhost:9200/_cat/nodes?v"
  * 查看索引：curl -X GET "localhost:9200/_cat/indices?v"
  * 创建索引：curl -X PUT "localhost:9200/test"
  * 删除索引：curl -X DELETE "localhost:9200/test"

* 使用postman查询

  * 提交数据，PUT localhost:9200/test/_doc/1选择Body,raw,JSON

  * 搜索，GET localhost:9200/test/_search?q=title(/content):xxx

  * 搜索时ES对关键词进行了分词

  * 通过请求体构造复杂搜索条件

## 2. Spring整合Elasticsearch

* 引入依赖
  * spring-boot-starter-data-elasticsearch
*  配置Elasticsearch
  * cluster-name、cluster-nodes（集群的名字，节点）
  * Redis和Es底层都用到了Netty,有启动冲突。解决：在CommunityApplication类加入初始化方法进行配置。
* Spring Data Elasticsearch(调用API)
  * ElasticsearchTemplate（集成了Es的CRUD方法）
  * ElasticsearchRepository（接口，底层为ElasticsearchTemplate，用起来更方便）

## 3. 开发社区搜索功能

### 搜索服务

- 将帖子保存至Elasticsearch服务器。
  - 对贴子实体类DiscussPost用注解进行相关配置
  - 从Mybatis取数据存入
  - 在dao层创建DiscussPostRepository类，继承ElasticsearchRepository接口即可，它集成了CRUD方法
- 从Elasticsearch服务器删除帖子。
- 从Elasticsearch服务器搜索帖子。
  - Es可以在搜索到的词加标签，达到高亮显示
  - 利用elasticTemplate.queryForPage()查询

### 发布事件

- 发布帖子时，将帖子异步的提交到Elasticsearch服务器。
  - 新建ElasticsearchService类，定义CRUD和搜索方法。
  - 在DiscussPostController类发帖时，定义和触发发帖事件（Event、eventProducer.fireEvent(event)）
- 增加评论时，将帖子异步的提交到Elasticsearch服务器。
  - 在CommentController类发表评论时，定义和触发发帖事件
- 在消费组件中增加一个方法，消费帖子发布事件。
  - 在EventConsumer类增加消费发帖事件的方法
  - 在事件中查询帖子，存到Es服务器

### 显示结果

- 在控制器中处理搜索请求，在HTML上显示搜索结果。
  - 新建SearchController类处理搜索请求
  - 此时为GET请求，keyword的传入（search?keyword=xxx）
  - 修改index.html,表单提交路径，文本框name="keyword"
  - 在search.html修改，遍历取到帖子。

### DEBUG

* 记得要在kafka创建新的TOPIC,坑爹的debug了好久。

