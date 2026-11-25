# pgsql如何安装卸载数据恢复

```javascript
查看
cat jdbc-connect.sh | grep curl 
删除kafka相关的
curl -XDELETE  http://192.168.20.207:31115/connectors/jdbc-kafka-sink
卸载
helm uninstall pgsql -n manager
清理数据
cd /teambition/db/pgsql
rm -rf ./*
重新安装
cd /teambition/manager/helm/
helm install pgsql ./postgresql-ha/ -n manager
进容器建库
kubectl  exec -it pgsql-postgresql-ha-postgresql-0  -n manager sh
psql -U postgres
密码是docker
create database kafka_connect;
恢复数据
kubectl exec -i pgsql-postgresql-ha-postgresql-0 -n manager -- /bin/sh -c 'export PGPASSWORD=docker && psql -U postgres -d kafka_connect ' < kafka_connect-2023-12-09-16-22.sql

扩展知识：进库,查看所有表，查看莫个人的详细数据；这是活跃度相关的数据。ID信息请在mongo数据里查看
\c kafka_connect
\dt
select * from user_active_fact where _id='568cdbff6dd8e2e67dff6b32'
```