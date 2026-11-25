# 缓存问题-grafana排查前端异常

# 缓存问题-grafana排查前端异常

通过grafana来排查前端异常

进入前端异常的dashboards

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/KM7qe9e8D7Zkqpj8/img/7d9ee1c3-b36c-492f-a116-db2ddb6b9e7a.png)

通过右上角小齿轮去配置message的变量，可以直接复制userkey的配置

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/KM7qe9e8D7Zkqpj8/img/8c4ab3bc-64e4-42d6-a30d-49c1c09edb3c.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/KM7qe9e8D7Zkqpj8/img/6e5cf417-6d82-4d33-b38e-652cb89a6f2f.png)

然后在上面可以看到message的变量配置入口；然后在下面的图形和日志条里有个三个点可以加入sql语句去搜索

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/KM7qe9e8D7Zkqpj8/img/7c44f746-6176-4643-8dc8-f5881043f0c9.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/KM7qe9e8D7Zkqpj8/img/afc8b3d7-a8ce-468c-969f-fcb914c08e57.png)

AND ('' = '${message}' OR message LIKE '%${message}%')

上下都要配置

时间选择目标时间区间

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/KM7qe9e8D7Zkqpj8/img/1b065a5f-530e-4e34-b797-4425a6e57e92.png)

这样就可以查到想要的message数据

---

方案提供者： $\color{#0089FF}{@金松}$