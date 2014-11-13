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
```
https://github.com/elasticsearch/elasticsearch.git
https://github.com/chenryn/kibana.git 
```
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
#### 7.遇到的问题,es内部队列满了导致search出错
![img](http://182.92.69.21/images/logstashImage/11.png)
解决的办法:threadpool.search.queue_size: -1,具体看自己机器的配置
#### 8.nginx,log日志的正则匹配
网站地址:http://grokdebug.herokuapp.com/
如:
```
10:18:50.699 [http-bio-8090-exec-3] DEBUG c.h.f.m.F.selectByDate - <==      Total: 10
```
正则:
```
%{TIME:time} %{SYSLOG5424SD:syslog5424sd} %{WORD:status} %{JAVACLASS:class}
```
结果:
```
{
  "time": [
    [
      "10:18:50.699"
    ]
  ],
  "syslog5424sd": [
    [
      "[http-bio-8090-exec-3]"
    ]
  ],
  "status": [
    [
      "DEBUG"
    ]
  ],
  "class": [
    [
      "c.h.f.m.F.selectByDate"
    ]
  ]
}
```
#### 9.logstash,agent
```
input {   
 file {   
   type => "nginx"   
   path => ["/usr/local/nginx/logs/*.log"]
   #type => "fund"
   #path => ["/usr/local/logstash/fund.*.log"]
   exclude => ["*.gz"]
   tags => ["nginx"]   
   start_position => beginning
 }   
}
output {   
 stdout {}
 redis {   
   host => "127.0.0.1"   
   data_type => "list"   
   key => "logstash"   
 }   
}
```
#### 9.logstash,index.conf
```
input {   
 redis {   
   host => "127.0.0.1"   
   port => "6379"    
   data_type => "list"   
   key => "logstash"   
 }   
}

#filter {
#    mutate { replace => { "type" => "nginx-log" } }
#    grok {
#      match => { "message" => "%{COMBINEDAPACHELOG}" }
#    }
#    date {
#      match => [ "timestamp" , "yyyy/MMM/dd:HH:mm:ss" ]
#    }
#}


#filter {
#    mutate { replace => { "type" => "fund" } }
#    grok {
#      match => { "message" => "%{URIHOST}:%{BASE16FLOAT} %{SYSLOG5424SD} DEBUG %{JAVACLASS}" }
#    }
#  date {
#    match => [ "timestamp" , "dd/MMM/yyyy:HH:mm:ss ZZZ" ]
#  }
#}

filter {
    mutate { replace => { "type" => "nginx_log" } }
    grok {
      match => { "message" => "%{COMBINEDAPACHELOG}" }
    }
    date {
      match => [ "timestamp" , "dd/MMM/yyyy:HH:mm:ss Z" ]
    }
    geoip {
      source => "clientip"
    }
}

output {
  elasticsearch {
    host => "127.0.0.1"
  }
  stdout { codec => rubydebug }
}
```

#### 10.运行,es、redis已启动,nginx配置好kibana的访问路径,logstash1.4.2下载完成
```
bin/logstash agent -f ../agent.conf &
bin/logstash agent -f ../index.conf &
```
![img](http://182.92.69.21/images/logstashImage/12.png)

