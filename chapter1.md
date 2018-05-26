# task1

\#==elk任务一==：

搭建一套elk,并导入一套英文名著作为测试。

\#\#  1. ELK简介

 Elasticsearch是一个分布式搜索引擎和分析引擎，用于数据存储，可提供实时的数据查询。



Logstash 负责从每台机器抓取日志数据，对数据进行格式转换和处理后，输出到Elasticsearch中存储



Kibana是一个数据可视化服务，根据用户的操作从Elasticsearch中查询数据，形成相应的分析结果，以图表的形式展现给用户。

\#\#\# 1.1 ELK一般架构模式

 kibana &lt;--- elasticsearch &lt;--- logstash 

 

