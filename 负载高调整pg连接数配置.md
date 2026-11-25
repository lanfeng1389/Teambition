# 负载高调整pg连接数配置

问题背景：181 版本 pg连接数配置设置的有问题，导致机器load负载高。

现象：

ps -ef | grep pgpool | wc -l

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/NpQlK5MwA0QYLqDv/img/e1eddbbe-f8be-4c90-a574-4fc45d8d7ac1.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/NpQlK5MwA0QYLqDv/img/739d39f9-86b6-4d84-807d-c0bbe4c0147e.png)

numInitChildren 设置的越大，pgpool子进程数越大，所以当numInitChildren 设置的很大的时候，即使实际的pg连接数不多，也会导致系统占用资源过高。而且最大连接池maxPool= 3 ，资源利用率没有最大化利用，maxPool 缓存连接池，是用户 + 数据库组合，例如【user1,db1】【user1,db2】算2个缓存连接。

# 调整pg连接数配置：

调整前：

maxConnections: 30000

maxPool: 3

numInitChildren: 7000

调整后：

1 更改 /teambition/midware/helm/postgresql-ha/helm/postgresql-ha/values.yaml 中

maxConnections: 6000 

maxPool=10 

numInitChildren=500

2 更新Pgsql

helm upgrade pgsql /teambition/midware/helm/postgresql-ha -n middleware