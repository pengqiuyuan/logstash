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
![img](http://182.92.69.21/images/logstashImage/1.png)
	
如图示，需要在分布式的Servers上通过**Logstash**的agent进行收集数据（使用**Redis**进行存储），然后由一台Server集中地从**Redis**中pull下来，建立索引，并结合**Elasticsearch**进行查询和统计。

## 展示
---
http://182.92.69.21/home/elasticsearch-rtf-master/elasticsearch-rtf-master/plugins/head/_site/index.html
http://182.92.69.21/kibana/src/index.html#/dashboard/file/logstash.json
#### 1. 整体展示,histogram
![img](http://182.92.69.21/images/logstashImage/2.png)
#### 2. 地图,map,map 面板把 2 个字母的国家或地区代码转成地图上的阴影区域。
![img](http://182.92.69.21/images/logstashImage/3.png)
#### 3. 请求位置分布详情展示,bettermap
![img](http://182.92.69.21/images/logstashImage/4.png)
#### 4. nginx日志的字段展示,table
![img](http://182.92.69.21/images/logstashImage/5.png)
![img](http://182.92.69.21/images/logstashImage/7.png)
#### 5. 线性histogram
![img](http://182.92.69.21/images/logstashImage/6.png)
#### 6. elasticsearch,我们需要的message、path、response、ip等等
![img](http://182.92.69.21/images/logstashImage/8.png)
![img](http://182.92.69.21/images/logstashImage/9.png)
![img](http://182.92.69.21/images/logstashImage/10.png)
#### 7. 整体展示,histogram



