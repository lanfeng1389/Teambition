# 突然停电导致系统进不去和mongo数据库恢复

```javascript
1. 执行这个命令修复xfs文件
xfs_repair  /dev/mapper/centos-root
reboot
-L参数删除日志
-d磁盘只读的情况下，挂载时直接跑，但有风险
mount -o remount,rw / 挂载
-----------------------------进入应急救援模式---------------------------
一、systemd.unit=emergency.target 
二、single
三、ro 改为 rw init=/sysroot/bin/sh
四、init=/bin/bash
乱码可以用这个：export LANG=en_US.UTF-8  

进入系统以后发现mongo数据库文件损坏，重新部署mongo数据库并导入数据
2. 挪走老的mongo相关的数据
mv /teambition/db/mongodb/ *  ~/
mv /teambition/conf/mongodb/ * ~/
mv /teambition/log/mongodb/ * ~/
cd /etc/kubernetes/manifests/
mv mongo.yaml ../
  
3. 进入容器执行playbook
docker exec -it ID sh							##tesmc_worker_1进这个容器
cd /code/data/apps/teambition/ansible
vi roles/mongodb/tasks/main.yaml     ##注释掉最后一条内容
ansible-playbook  -i /code/data/inventory/ install-mongo.yaml -e '@/code/data/inventory/teambition_vars.yaml'

4. 导入数据恢复数据库
cd /teambition/data/databak/mongo-对于的时间节点
for  i in `ls ./ | grep  -Ev 'oplog|admin'`;do  mongorestore -u root -p xxxx --authenticationDatabase admin -d $i ./$i  --noIndexRestore ;done
```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/ybEnBBBK03QdnP13/img/ff7d572e-5c31-42ea-bd53-5241342f7646.png)

![æªå±2024-03-18 15.01.39.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/ybEnBBBK03QdnP13/img/69fec645-f186-43c3-8aaf-ab271ddef244.png)