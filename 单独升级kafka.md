# 单独升级kafka

背景：先正达通过阿里云平台扫描工具扫描到CVE-2024-31141漏洞

当前版本 registry.teambition.net/library/kafka-mix:3.4.0-debian-11-r15升级至 registry.teambition.net/library/kafka-mix:3.9.0-debian-12-r13可解决此漏洞

## 升级步骤

### 1、备份

cp -r /teambition/db/kafka-zookeeper /tmp/

### 2、修改配置文件

```plaintext
vi /teambition/midware/helm/kafka/helm/kafka/values.yaml
```

![image](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/mxPOG5NJ47QRKnKa/img/e306f13a-46b0-4d35-b97b-0bb81e4748db.png)

### 3、重启服务

```plaintext
cd /teambition/midware/helm/kafka/helm 
helm upgrade kafka kafka -n middleware
```