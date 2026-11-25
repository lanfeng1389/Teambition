# mongodb 升级

# 1 背景

当前私有客户mongodb 部署版本为4.0.3 和 4.2.0 ，该版本存在 CVE-2025-14847漏洞，漏洞详情：[https://www.cve.org/CVERecord?id=CVE-2025-14847](https://www.cve.org/CVERecord?id=CVE-2025-14847)，需要升级至 **4.4.3** 版本进行漏洞修复。

4.4.3 版本mongo镜像： registry.teambition.net/library/mongo-amd64:4.4.30

arm版本：registry.teambition.net/library/mongo-arm64:4.4.30

# 2 mongodb（4.0.3） 升级

当mongodb 版本是 4.0.3 升级到 4.4.3 ，需要先升级到4.2.0过渡 ，再升级到4.4.3 。

升级镜像：

registry.teambition.net/library/mongo-mix:4.2.0

registry.teambition.net/library/mongo-amd64:4.4.30

### 升级步骤 (mongodb升级只需要替换镜像即可，无配置更改)：

#### 2.1 升级前务必备份mongodb数据，再进行mongo服务升级。

#### 2.2 停止mongodb服务

mv /etc/kubernetes/manifests/mongo.yaml  /root/

替换镜像版本为registry.teambition.net/library/mongo-mix:4.2.0：

vi /root/mongo.yaml

#### 2.3 启动mongodb

mv /root/mongo.yaml /etc/kubernetes/manifests

#### 2.4 进入 mongo shell，执行以下命令，将数据库的兼容性版本设置为 4.2

use admin

db.adminCommand( { setFeatureCompatibilityVersion: "4.2" } )

确认是否生效：

db.adminCommand( { getParameter: 1, featureCompatibilityVersion: 1 } )

#### 2.5 停止mongodb，再升级至4.4.3 

mv /etc/kubernetes/manifests/mongo.yaml  /root/

替换镜像版本为registry.teambition.net/library/mongo-amd64:4.4.30

vi /root/mongo.yaml

mv /root/mongo.yaml /etc/kubernetes/manifests

#### 2.6 升级至 4.4.3后，开启 4.4 特性

use admin

db.adminCommand( { setFeatureCompatibilityVersion: "4.4" } )

确认是否生效：

db.adminCommand( { getParameter: 1, featureCompatibilityVersion: 1 } )

#### 2.7 升级Mongodump命令（务必检查）

检查日常crontab备份脚本中的dump版本是否为4.4，直接使用备份脚本中的备份命令进行一次测试备份，如果报错，则需要替换备份命令。（必须测试）

kubectl cp mongo-master1.teambition.local:/usr/bin/mongodump -n kube-system  /usr/local/bin/mongodump

测试备份是否成功

# 3 mongodb （4.2.0） 升级

### 3.1 升级前全量备份mongo数据，再进行mongo服务升级。

### 3.2 停止mongo服务

mv /etc/kubernetes/manifests/mongo.yaml  /root/

### 3.3 修改镜像版本为 registry.teambition.net/library/mongo-amd64:4.4.30

vi /root/mongo.yaml

### 3.3 启动mongo服务

mv /root/mongo.yaml /etc/kubernetes/manifests

#### 3.4 升级至 4.4.3后，开启 4.4 特性

use admin

db.adminCommand( { setFeatureCompatibilityVersion: "4.4" } )

确认是否生效：

db.adminCommand( { getParameter: 1, featureCompatibilityVersion: 1 } )

#### 升级Mongodump命令（务必检查）

检查日常crontab备份脚本中的dump版本是否为4.4，直接使用备份脚本中的备份命令进行一次测试备份，如果报错，则需要替换备份命令。（必须测试）

kubectl cp mongo-master1.teambition.local:/usr/bin/mongodump -n kube-system  /usr/local/bin/mongodump

测试备份是否成功