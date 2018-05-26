# ==elk任务一==：

搭建一套elk,并导入一套英文名著作为测试。

==elk任务一==：

1. ELK简介

  1.1 ELK一般架构模式

2. ELK测试部署

  2.1 机器准备

  2.2 elk准备

   2.2.1 elasticsearch

      1）elasticsearch安装
    
     2）elasticsearch基本配置
    
    3）elasticsearch启动
    
    3）elasticsearch插件设置 

  2.2.2 logstash dx

   1）logstash安装

   2\) logstash配置之编写conf文件

   3\) logstash启动

  2.2.3 kinaba

   1）kinaba安装
 
  2）kinaba配置
 
  3）kinaba启动
 
  4）kinaba索引

3. 测试总结

## 1. ELK简介

Elasticsearch是一个分布式搜索引擎和分析引擎，用于数据存储，可提供实时的数据查询。

Logstash 负责从每台机器抓取日志数据，对数据进行格式转换和处理后，输出到Elasticsearch中存储。

Kibana是一个数据可视化服务，根据用户的操作从Elasticsearch中查询数据，形成相应的分析结果，以图表的形式展现给用户。

### 1.1 ELK一般架构模式

kibana &lt;--- elasticsearch &lt;--- logstash

kibana &lt;--- elasticsearch &lt;--- logstash &lt;--- redis/kafka &lt;--- logstash

测试环境比较简单，压力需求不大，故这里选择第一种模式用两台做测试。

## 2. ELK测试部署

### 2.1 机器准备

两台虚拟机：

| hostname | ip地址 | 系统 | 软件 |
| :--- | :--- | :--- | :--- |
| linux-node1 | 10.0.0.7 | centos6 | elk+java |
| linux-node2 | 10.0.0.6 | centos6 | el +java |

英文名著pride\_and\_prejudice（《傲慢与偏见》） 做处理数据放在 /tmp下。

![](/assets/import.png)

### 2.2 elk准备

#### 2.2.1  elasticsearch

#### 1）elasticsearch安装

elasticsearch 需要java环境。

```
 yum install java -y 
 java -version 
openjdk version "1.8.0_171"
```

下载并安装GPG key

```
 rpm --import https://packages.elastic.co/GPG-KEY-elasticsearch
```

添加yum仓库

```
 vim /etc/yum.repos.d/elasticsearch.repo
[elasticsearch-2.x]
name=Elasticsearch repository for 2.x packages
baseurl=http://packages.elastic.co/elasticsearch/2.x/centos
gpgcheck=1
gpgkey=http://packages.elastic.co/GPG-KEY-elasticsearch
enabled=1
```

yum安装elasticsearch

```
  yum install -y elasticsearch
  rpm -qa| grep ela
elasticsearch-2.4.6-1.noarch
```

#### 2）elasticsearch基本配置

**修改node1配置文件**。

```
grep -n '^[a-Z]' /etc/elasticsearch/elasticsearch.yml  
17:cluster.name: elk-test    判别节点是否是统一集群
23:node.name: elk-node-1     节点的hostname
33:path.data: /data/es-data  数据存放路径
37:path.logs: /var/log/elasticsearch/ 日志路径
43:bootstrap.memory_lock: true 锁住内存，使内存不会再swap中使用
54:network.host: 10.0.0.7 主机IP 
58:http.port: 9200 端口
95:discovery.zen.ping.unicast.hosts: ["10.0.0.7", "10.0.0.6"] 这里采用单播模式
```

授权，否则报错。

```
 mkdir -p /data/es-data
 chown  elasticsearch.elasticsearch /data/es-data/
```

#### 3）elasticsearch启动

```
 /etc/init.d/elasticsearch start
 chkconfig --add elasticsearch
 netstat -lntup|grep 9200
tcp        0      0 ::ffff:10.0.0.7:9200        :::*                        LISTEN
```

同理，修改node2配置文件。

#### 3）elasticsearch插件设置

elasticsearch有许多插件及针对应用，具体可以详见插件资料。  
  这里安装一个head插件来显示索引和分片情况。

```
    /usr/share/elasticsearch/bin/plugin install mobz/elasticsearch-head
```

访问测试安装。

```
http://10.0.0.7:9200/_plugin/head/
```

![](/assets/import1.png)

### 2.2.2 logstash

#### 1）logstash安装

下载并安装GPG KEY

```
  rpm --import https://packages.elastic.co/GPG-KEY-elasticsearch
```

添加yum仓库

```
  vim /etc/yum.repos.d/logstash.repo
[logstash-2.1]
name=Logstash repository for 2.1.x packages
baseurl=http://packages.elastic.co/logstash/2.1/centos
gpgcheck=1
gpgkey=http://packages.elastic.co/GPG-KEY-elasticsearch
enabled=1
```

yum安装 logstash

```
  yum install -y  logstash
  rpm -qa| grep  logstash
logstash-2.1.3-1.noarch
```

#### 2\) logstash配置之编写conf文件

logstash配置文件包含三个配置部分，分别为：input{}、filter{}、output{}。  
这里编写conf文件如下：

```
input {
    file{
        path => ["/tmp/pride_and_prejudice.txt"]
        type => "txt-test"
        start_position => "beginning"
}

}


filter{
}

output{
    elasticsearch {
        hosts => ["10.0.0.7:9200"]
        index => "txt-test-%{+YYYY.MM}"
    }
}
```

#### 3\) logstash启动

```
 /opt/logstash/bin/logstash -f /etc/logstash/conf.d/test-txt.conf
```

启动成功后可以在head中看到已经建立索引信息了。

![](/assets/import3.png)![](/assets/import4.png)

### 2.2.3 kinaba

#### 1）kinaba安装

安装同上

```
rpm --import https://packages.elastic.co/GPG-KEY-elasticsearch
```

```
 vim /etc/yum.repos.d/kibana.repo 
[kibana-4.5]
name=Kibana repository for 4.5.x packages
baseurl=http://packages.elastic.co/kibana/4.5/centos
gpgcheck=1
gpgkey=http://packages.elastic.co/GPG-KEY-elasticsearch
enabled=1
```

```
yum install -y kibana
rpm -qa kibana
kibana-4.5.4-1.x86_64
```

#### 2）kinaba配置

kibana只需要配置连接ES即可。

```
vim /opt/kibana/config/kibana.yml
grep "^[a-Z]" /opt/kibana/config/kibana.yml
server.port: 5601
server.host: "0.0.0.0"
elasticsearch.url: "http://10.0.0.7:9200"
kibana.index: ".kibana"
```

#### 3）kinaba启动

```
/etc/init.d/kibana start
```

```
netstat -lntup |grep 5601
tcp        0      0 0.0.0.0:5601                0.0.0.0:*                   LISTEN      4756/node
```

```
访问地址：http://10.0.0.7:5601
```

#### 4）kinaba索引

kibana上不会把es上所有的索引显示出来，需要我们配置那个索引，显示那个索引。  
下面示例创建一个txt-test\* 索引,并选择关键字yes作为搜索且保存为新的索引。  
![](/assets/import5.png)  
![](/assets/import6.png)  
搜索yes关键字  
![](/assets/import7.png)  
![](/assets/import8.png)  
![](/assets/import9.png)  
如上所示我们可以将关键搜索索引保存以方便下载加载使用。  
上面测试使用英文文本名著做测试，实际生产环境中可以匹配定位error等关键字。  
![](/assets/import12.png)

## 3.  测试总结

如上我们通过名著文本完成类似日志收集分析的搭建。  
 实际生产环境根据扩展集群规模，收集系统各类日志，安装需要的插件进行分析监控等，大体原理上是类似的。

