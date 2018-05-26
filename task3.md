# ==elk 任务三==：

将logstash改成fluentd的可行性，以及实现步骤。



“日志收集，参考社区推荐方式：

使用了Elasticsearch + fluentd + Kibana的组合，

fluentd作为Rancher的Global Serivce（对应于Kubernetes的daemon set），收集每台机器的系统日志，dockerd日志，通过docker\_metadata这个插件来收集容器标准输出\(log\_driver: json\_file\)的日志，

rancher基础服务日志，既本地文件系统压缩存档也及时地发往相应的elasticsearch服务（并未用容器方式启动），

通过Kibana可视化供产品售后使用。基于的日志报警使用的是Yelp开源的elastalert工具。”

注意：重要应用机器的数据应当做好备份，时间的选择、压力的预估，以及迁移时考虑做好标记点导入对接下线。

参考：

[https://segmentfault.com/a/1190000000730444](https://segmentfault.com/a/1190000000730444)

[https://help.aliyun.com/document\_detail/66659.html?spm=a2c4e.11153959.blogcont448676.22.292b3cfcJ5uDFM](https://help.aliyun.com/document_detail/66659.html?spm=a2c4e.11153959.blogcont448676.22.292b3cfcJ5uDFM)

[https://denibertovic.com/post/docker-and-logstash-smarter-log-management-for-your-containers/](https://denibertovic.com/post/docker-and-logstash-smarter-log-management-for-your-containers/)

