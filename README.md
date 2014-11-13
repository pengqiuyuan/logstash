---
layout: page
title: Logstash, Elasticsearch, Kibana处理日志
tag: [ Logstash, Elasticsearch, Kibana, 日志, 数据, 可视化, 安装, 配置, 坑 ]
---
**使用环境:**

- **elasticsearch version 1.1.1**
- **logstash version 1.4.2**
- **kibana 3.1.0**
- **redis 2.6+**

>https://github.com/elasticsearch/elasticsearch.git &&
>https://github.com/chenryn/kibana.git 

## 两个主要的使用场景:
---
#### 1. Standalone server:
使用**Logstash**和内部集成的**Elasticsearch**和**Kibana**，足以胜任在一台单独的Server上分析和实时统计各种格式的日志文件。
	
#### 2. Centralized server: 
![image](http://michael.bouvy.net/blog/wp-content/uploads/2013/11/logstach-archi1.png)  
	
如图示，需要在分布式的Servers上通过**Logstash**的agent进行收集数据（使用**Redis**进行存储），然后由一台Server集中地从**Redis**中pull下来，建立索引，并结合**Elasticsearch**进行查询和统计。




