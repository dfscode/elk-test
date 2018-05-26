#==elk任务一==：
搭建一套elk,并导入一套英文名著作为测试。
##  1. ELK简介
 Elasticsearch是一个分布式搜索引擎和分析引擎，用于数据存储，可提供实时的数据查询。

Logstash 负责从每台机器抓取日志数据，对数据进行格式转换和处理后，输出到Elasticsearch中存储

Kibana是一个数据可视化服务，根据用户的操作从Elasticsearch中查询数据，形成相应的分析结果，以图表的形式展现给用户。
### 1.1 ELK一般架构模式
 kibana <--- elasticsearch <--- logstash 
 
 kibana <--- elasticsearch <--- logstash <--- redis/kafka <--- logstash
 
 测试环境比较简单，压力需求不大，故这里选择第一种模式用两台做集群测试。
 
 ## 2. ELK测试部署
 ### 2.1 机器准备
两台虚拟机：
hostname | ip地址|系统|软件
---|---|---|---|
linux-node1| 10.0.0.7|centos6|
linux-node2|10.0.0.6|centos6|


 ###  2.2 elk准备
   #### 2.2.1  elasticsearch
   #### 1）elasticsearch安装
 elasticsearch 需要java环境。
 ``` 
 yum install java -y 
 java -version 
openjdk version "1.8.0_171"
 ```

 下载并安装GPG key 
 ```shell
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
grep -n '^[a-Z]'
/etc/elasticsearch/elasticsearch.yml  
17:cluster.name: elk-test    判别节点是否是统一集群
23:node.name: elk-node-1     节点的hostname
33:path.data: /data/es-data  数据存放路径
37:path.logs: /var/log/elasticsearch/ 日志路径
43:bootstrap.memory_lock: true 锁住内存，使内存不会再swap中使用
54:network.host: 10.0.0.7 允许访问的ip 
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
 
 同上，修改node2配置文件，并且
   #### 3）elasticsearch插件设置
  elasticsearch有许多插件及针对应用，具体可以见附件资料。
  这里安装一个head插件来显示索引和分片情况。
    
```
    /usr/share/elasticsearch/bin/plugin install mobz/elasticsearch-head
```
访问测试安装。
```
http://10.0.0.7:9200/_plugin/head/
```
![image](http://note.youdao.com/noteshare?id=2ff1fdd18ca82dfe38faef8f64260ae5&sub=5335B6D9A4404377B646D23927C56365)

  ####  2.2.2 logstash
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
   #### 2) logstash配置之编写conf文件
  ###  2.2.3 kinaba
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
# 3.  测试环境效果
   ## 导入名著 kinaba显示
 
 elk 任务二： 对比ELK中logstash和fluentd的优缺点
 
 elk 任务三：将logstash改成fluentd的可行性，以及实现步骤。
 
 