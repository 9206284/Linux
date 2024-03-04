# Logstash安装与使用

## 一、 介绍

集中、转换和存储数据

logstash是使用jruby实现的，非常耗资源。

<https://www.elastic.co/cn/products/logstash>

Logstash 是开源的服务器端数据处理管道，能够同时从多个来源采集数据，转换数据，然后将数据发送到您最喜欢的 “存储库” 中。（我们的存储库当然是 Elasticsearch。）

## 二、安装

### 1. 导入公钥

```shell
sudo rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
```

### 2. 添加yum源

把以下内容添加至/etc/yum.repos.d/目录下，文件以.repo结尾，例如：`logstash.repo`

```shell
[logstash-7.x]
name=Elastic repository for 7.x packages
baseurl=https://artifacts.elastic.co/packages/7.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
```

安装源为官方提供，速度比较慢，可换为国内镜像网站的地址，如清华大学开源镜像站的地址：https://mirrors.tuna.tsinghua.edu.cn/elasticstack/yum/elastic-7.x/

### 3. 安装Logstash

```shell
~]# yum install -y logstash
```

### 4. 配置文件

jvm参数配置：/etc/logstash/jvm.options

日志配置：/etc/logstash/log4j2.properties

把自己写好的配置文件，统一放在 `/etc/logstash/` 目录下(注意目录下所有配置文件都应该是 **.conf** 结尾，且不能有其他文本文件存在。因为 logstash agent 启动的时候是读取全文件夹的)，然后运行 `systemctl start logstash` 命令即可。

## 三、 配置讲解

### 1. 配置段

Logstash分三个配置段，分别是input, filter, output

示例：

```shell
input {								# 输入插件，定义接收从何而来的文件流。支持多种插件，如stdin, redis, kafka, file, beats等多种插件
	PluginName {
        
	}
}

filter {							# 过滤器插件，对input输入的原始数据进行各种处理。
    
}

output {							# 输出插件。定义处理后的日志输出至何处。
	stdout {
		codec => rubydebug 			# 输出使用codec参数，并指明值的格式为rubydebug
	}
}
```

### 2. 配置示例：

#### 2.1 从标准输入收集日志：

```shell
input {
        stdin {
        
        }
}
output {
        stdout {
                codec => rubydebug
        }
}

# 输出
{
    "@timestamp" => 2021-05-29T09:32:29.271Z,
          "host" => "logstash",
      "@version" => "1",
       "message" => "hello elk"
}
```



#### 2.2 file插件

path：读取文件的路径，数据类型是array，需要加中括号

start_position：文件从哪儿读，可用beginning（文件读取停下之后，下次从停下的位置开始读）、end

delimiter：指定分行符，默认换行符

```shell
input {
        file {
              path => ["/var/log/httpd/access_log"]
              start_position => "beginning"
        }
}
output {
        stdout {
                codec => rubydebug
        }
}
```

输出：

```shell
{
          "path" => "/var/log/httpd/access_log",
          "host" => "logstash",
      "@version" => "1",
       "message" => "192.168.100.1 - - [29/May/2021:17:55:47 +0800] \"GET / HTTP/1.1\" 304 - \"-\" \"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.93 Safari/537.36\"",
    "@timestamp" => 2021-05-29T09:55:48.444Z
}
```

#### 2.3 grok

解析文本并进行结构化处理。

日志信息格式都一样，但是整个信息是一行，分别加键，用正则表达式分析每一行，做特定匹配，对每个特定匹配加不同的键。每次匹配都取出来不同的内容，加不同的键。

grok能实现把对应的文本信息读出来之后，额外单独加键。

##### 2.3.1 示例1

日志：

```
55.3.244.1 | GET | /index.html | 15824 | 0.043
```

pattern：

```
%{IP:client} %{WORD:method} %{URIPATHPARAM:request} %{NUMBER:bytes} %{NUMBER:duration}
```

logstash配置：

```shell
input {
	file {
		path => "/tmp/tmpfile.log"
		}
}
filter {
	grok {
		match => { "message" => "%{IP:client} %{WORD:method} %{URIPATHPARAM:request} %{NUMBER:bytes} %{NUMBER:duration}" }
	}
}
output {
        stdout {
                codec => rubydebug
        }
}
```

经过filter处理后，会输出如下信息：

```
{
      "@version" => "1",
        "client" => "55.3.244.1",
    "@timestamp" => 2021-05-29T10:17:32.599Z,
        "method" => "GET",
       "request" => "/index.html",
         "bytes" => "15824",
          "path" => "/tmp/tmpfile.log",
      "duration" => "0.043",
          "host" => "logstash",
       "message" => "55.3.244.1 GET /index.html 15824 0.043"
}
```

gork patterns：http://grokdebug.herokuapp.com/patterns#

正则表达式库：https://github.com/kkos/oniguruma/blob/master/doc/RE

##### 2.3.2 示例2：自定义pattern

格式：

```
(?<field_name>the pattern here)
```

自定义pattern(/etc/logstash/conf.d/patterns)：

```
REQUESTMETHOD \b[A-Z]{,5}\b
```

```shell
input {
	file {
		path => "/tmp/tmpfile.log"
		}
}
filter {
	grok {
		patterns_dir => "/etc/logstash/conf.d/patterns"
		match => { "message" => "%{IPV4:clientip} %{REQUESTMETHOD:request_method} %{URIPATH:request_uri} %{INT:bytes}" }
	}
}
output {
        stdout {
                codec => rubydebug
        }
}
```

##### 2.3.3 内建键

有些正则表达式匹配非常困难，grok加入了一些内建的键。如COMBINEDAPACHELOG（apache日志）

示例：

```shell
input {
        file {
              path => ["/var/log/httpd/access_log"]
              start_position => "beginning"
        }
}
filter {
        grok {
               match => {
               "message" => "%{COMBINEDAPACHELOG}"
              }
         }
}
output {
        stdout {
                codec => rubydebug
        }
}
```

输出：

```shell
{
           "path" => "/var/log/httpd/access_log",
           "host" => "logstash",
       "@version" => "1",
       "clientip" => "192.168.100.1",
    "httpversion" => "1.1",
           "auth" => "-",
       "response" => "304",
        "request" => "/",
          "ident" => "-",
      "timestamp" => "29/May/2021:17:49:43 +0800",
          "agent" => "\"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.93 Safari/537.36\"",
     "@timestamp" => 2021-05-29T09:49:43.993Z,
       "referrer" => "\"-\"",
        "message" => "192.168.100.1 - - [29/May/2021:17:49:43 +0800] \"GET / HTTP/1.1\" 304 - \"-\" \"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.93 Safari/537.36\"",
           "verb" => "GET"
}
```

#### 2.5 删除多余的行

示例：

```shell
input {
        file {
              path => ["/var/log/httpd/access_log"]
              start_position => "beginning"
        }
}
filter {
         grok {
               match => {
               "message" => "%{HTTPD_COMBINEDLOG}"
               }
         remove_field => "message"
         }
}
output {
        stdout {
                codec => rubydebug
        }
}
```

#### 2.6 datafiler插件

把timestamp转换为@timestamp的格式并存入@timestamp内

@timestamp" => 2018-06-20T14:50:05.061Z,		#获取日志时间

timestamp" => "20/Jun/2018:22:50:04 +0800		 #日志中字段timestamp，生成日志的时间。

data插件的作用是把日志中的时间转换为@timestamp的格式并存入@timestamp内，并把timestamp删除

```shell
input {
        file {
              path => ["/var/log/httpd/access_log"]
              start_position => "beginning"
        }
}
filter {
         grok {
               match => {
               "message" => "%{HTTPD_COMBINEDLOG}"
               }
         }
         date {
                match => ["timestamp","dd/MMM/YYYY:H:m:s Z"]
                remove_field => "timestamp"
               }
}
output {
        stdout {
                codec => rubydebug
        }
}
```

#### 2.6 从beats收集日志并输出至elasticsearch：

```shell
input {
        beats {
                port => "5044"
        }
}
output {
        elasticsearch {
                hosts => "192.168.100.43"
                index => "logstash-%{+YYYY.MM.dd}"
        }
}
```

elasticsearch配置

```ini
node.name: node-1
path.data: /var/lib/elasticsearch
path.logs: /var/log/elasticsearch
network.host: 192.168.100.11
http.port: 9200
discovery.seed_hosts: ["192.168.100.11"]
```

logstash配置

```ini
- type: log
  enabled: true
  paths:
    - /var/log/httpd/access_log
# ------------------------------ Logstash Output -------------------------------
output.logstash:
  # The Logstash hosts
  hosts: ["localhost:5044"]
```

查看生成的索引：

```shell
~]# curl -XGET http://192.168.100.43:9200/_cat/indices
yellow open logstash-2021.05.29 s_DLkrkNQUGH1Xh54gfQbw 1 1 15 0 71.8kb 71.8kb
```







