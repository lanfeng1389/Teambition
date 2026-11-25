# DMZ nginx squid安装

> 确保机器上make, g++已安装

> yum install -y gcc-c++ make g++ build-essential

> 注意：如果客户电脑有yum源，可以直接用 yum install nginx,  yum install squid  安装，更简单，不需要编译安装

### 3.3.1 nginx安装配置

[请至钉钉文档查看附件《nginx-1.24.0.tar.gz》。](https://alidocs.dingtalk.com/i/nodes/N7dx2rn0JbxOaqnACQ4gbvxaWMGjLRb3?doc_type=wiki_doc&iframeQuery=anchorId%3DX02lzjhap4q2hw9rshuksl)

1 编译安装

```shell
1. 登录代理机器，上传nginx-1.24.0.tar.gz到用户家目录下（～/）
2. 解压tar包
cd ～/
tar -xzf nginx-1.24.0.tar.gz
3. 进入安装目录，执行编译安装
cd nginx-1.24.0
./configure --prefix=/home/{{user}}/nginx --user={{user}} --group={{group}} --with-http_v2_module --with-http_ssl_module
make & make install
```

2 修改配置文件

编辑nginx.conf文件：vi /home/{{ user }}/nginx/conf/nginx.conf  

修改如下：添加一行配置 include  conf.d/\*.conf;

注释35行 — 79行 server块

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8oLl9y4A74BdlapY/img/3a7bdc60-db58-4219-810f-bd41d2db1912.png)

修改 worker\_processes 为 auto, 修改worker\_connections 51200

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/jP2lRYjar10gPO8g/img/e7ec21e2-748f-4d12-848c-8b703b3e0ce3.png)

3 新增web.conf配置：

```shell
1. 创建conf.d目录
mkdir -p /home/{{user}}/nginx/conf/conf.d
2. 新建web.conf配置文件，替换以下web.conf.tpl变量值后将内容拷贝到web.conf文件中
vi /home/{{user}}/nginx/conf/conf.d/web.conf
3.新增 upstream.conf
vi /home/{{user}}/nginx/conf/conf.d/upstream.conf

```
```shell
server {
   listen 9080  ;
   server_name {{ tb.public.cn }};  

   #if ($http_user_agent !~* "Dingtalk") {
   #  return 403;
   #}

  location / {
   proxy_pass http://tb-server:80;
   proxy_set_header TB-Host $http_host;
   proxy_set_header Referer $http_referer;
   proxy_set_header Upgrade $http_upgrade;
   proxy_set_header Connection "upgrade";
   proxy_set_header X-Real-IP $remote_addr;
   proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
   proxy_set_header X-Forwarded-Proto $scheme;
   proxy_set_header Host "{{ tb.private.cn }}"; 
  }
}
```
```Shell
upstream tb-server {
    server 192.x.x.x:80 fail_timeout=60s;
    server 192.x.x.x:80 fail_timeout=60s;
    server 192.x.x.x:80 fail_timeout=60s;
}
```

4 启动

```shell
1. 进入执行目录
cd /home/{{user}}/nginx/sbin
2. 执行启动命令
./nginx
```

5 验证nginx 是否启动成功

```shell
1. 执行命令查看9080端口是否正常监听
netstat -tunlp | grep 9080
```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8oLl9y4A74BdlapY/img/b6b50e2a-8109-4c9f-930f-3c1b7c66738f.png)

6 开机自启动

vi /etc/rc.local

增加： /home/{{ user }}/nginx/sbin/nginx

### 3.3.2 squid安装

[请至钉钉文档查看附件《squid-5.3.tar.gz》。](https://alidocs.dingtalk.com/i/nodes/N7dx2rn0JbxOaqnACQ4gbvxaWMGjLRb3?doc_type=wiki_doc&iframeQuery=anchorId%3DX02lzjfo5g5xjn8o6fwky)

1）编译安装

```shell
1. 登录代理机器，上传squid-5.3.tar.gz到用户家目录下（～/）
2. 解压
cd ～/
tar -xzf squid-5.3.tar.gz
3. 进入安装目录，执行编译安装
cd squid-5.3/
./configure --prefix=/home/{{user}}/squid  --enable-ssl --enable-snmp --enable-cache-digests --enable-icmp --enable-delay-pools --enable-useragent-log --with-maxfd=65535 --with-filedescriptors=65535
make && make install
```

2）修改配置文件

vi /home/{{ user }}/squid/etc/squid.conf 

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/jP2lRYjar10gPO8g/img/0343e937-bc22-49ec-b9dc-1186d974f06d.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/jP2lRYjar10gPO8g/img/593db33c-40fb-4fa9-b071-ca2ef248c081.png)

增加：

cache\_effective\_user nobody

cache\_effective\_group nobody

3）启动

```shell
1. 进入执行目录
cd /home/{{user}}/squid/sbin
2. 执行启动命令
./squid -s
```

4）验证squid服务是否启动成功

```shell
1. 执行命令查看8128端口是否正常监听
netstat -tunlp | grep 8128
```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8oLl9y4A74BdlapY/img/a4203c2d-ecbe-47a2-bf51-432839fc204e.png)

5) 开机自启动

vi /etc/rc.local

增加： /home/{{ user }}/squid/sbin/squid