# Teambition私有部署安全功能

# 1 功能性安全

### 1.1 身份安全

##### 1.1.1 帐号连续5次登录失败，应锁定60分钟。

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/vBPlNY7zXQLBOdG8/img/dad59123-e4b1-42ae-9845-4c76bed7f64d.png)

##### 1.1.2 注册：图形验证码

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/vBPlNY7zXQLBOdG8/img/8e821f6d-33f1-4155-88e7-68de88d750e6.png)

##### 1.1.3 二步验证

登录时支持通过谷歌验证器进行二步验证，默认关闭，运维修改全局大values配置文件可打开

##### 1.1.4 登录失败，只返回“用户名或密码错误”

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/vBPlNY7zXQLBOdG8/img/650b399f-1cc3-46cb-b905-70b15cfbccf8.png)

##### 1.1.5 登录的用户再次登录时，须强制结束之前的登录会话，防止同一用户多人登录

默认关闭，运维修改全局大values配置文件可打开

##### 1.1.6 密码长度及复杂度可以自定义配置：默认是1大写+1数字 8位

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/vBPlNY7zXQLBOdG8/img/961a237b-9c6a-491d-8b77-e915f2e7d69e.png)

##### 1.1.7 密码强制修改周期：90天

默认关闭，运维修改全局大values配置文件可打开

### 1.2 操作日志

每周最多 3 次数据导出请求，任务完成时我们将邮件通知您，您也可以在「导出申请」中查看。可以按照成员或者操作类型进行过滤。

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/vBPlNY7zXQLBOdG8/img/41ebad1a-2841-40fb-b9c7-ce41eaa815b5.png)

在「操作日志」中可以统一查看企业内各项操作的操作记录。可以筛选时间段、成员和操作类型。管理后台的操作记录仅记录主要操作。

### 1.3 导出申请

![image](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/WgZOZwaZ3mpelLX8/img/6a98122d-9962-437a-9383-8526a03a4d8b.png)

1、完整日志导出操作：不设置任何筛选条件，直接点击右上角【导出】按钮，导出最近一年的全部操作记录 (单次导出最长不得超过一年)。

2、按条件导出假设我们要导出 Teambition 在 2018 年 6 月 14 日的全部操作记录，需要将 2 个时间段都设置为 14 日。选择【导出】，弹窗会提醒每周最多 3 次导出以及当前剩余次数。

确认后，可以在「导出申请」中查看。如果是已完成状态，\[导出申请\] 字段右侧会有一个小蓝点。鼠标悬浮在 “查看导出条件” 处可以再次查看导出条件，确认信息无误，即可点击下载按钮下载当前申请的数据记录。

### 1.4 安全水印

开启安全水印后，系统的所有页面都会显示水印。默认关闭，开启后内容为登录人账号

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/vBPlNY7zXQLBOdG8/img/91700de8-ecbd-4937-95ee-8c9608949515.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/vBPlNY7zXQLBOdG8/img/aa2f07ad-4a5b-49fb-bab4-aaede1666a44.png)

### 1.5 部门添加权限

开启部门添加权限后，项目邀请成员仅同步当前所属及以下部门。

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/vBPlNY7zXQLBOdG8/img/3d18b34d-34a1-49fe-be4c-26b79c67a576.png)

### 1.6 访问控制-角色权限

可以配置企业级角色和项目级角色。企业级角色缺省值为拥有者、管理员、成员、外部成员，可以新增自定义角色。项目级角色缺省值为项目拥有者、项目管理员、项目成员、任务访客，可以新增自定义角色。

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/vBPlNY7zXQLBOdG8/img/d85b95aa-3cb6-430e-b698-66abaff112e5.png)

### 1.7 SSO

*   支持与钉钉和企业微信进行集成，支持钉钉端内打开TB、企业微信打开TB、钉钉端扫码打开TB、企业微信扫码登录。
    
    ![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/vBPlNY7zXQLBOdG8/img/fda88f32-0431-492a-b753-4742863944a9.png)
    
*   支持与企业的LDAP集成，对接登录和集成。
    
*   支持Oauth2.0协议的SSO系统对接。需要二开。
    

### 1.8 钉钉离职人员如何禁用TB

TB管理后台--第三方平台管理，点击设置，账号未能对应钉钉账号时，同步策略改为停用或离职

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/vBPlNY7zXQLBOdG8/img/a888e720-1979-4d81-9d8f-17aec8aa0ac4.png)

### 1.9 安全围栏

> 安全围栏用于允许/限制文件下载访问等，默认是不打开安全围栏的

##### 设置管控规则

在添加规则时需先选择管控条件，选择管控条件后继续选择具体管控项，可正向管控（禁止），也可反向管控（允许）。

![image](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/WgZOZwaZ3mpelLX8/img/94a5cd4e-b680-4170-8503-c0cddb197d6b.jpg)

##### 单条管控规则内的多条管控条件

可在同一条规则内添加多条管控条件，条件和条件之间是「且」的关系。

![image](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/WgZOZwaZ3mpelLX8/img/0e7d3659-9699-470b-b43d-7f5d96993a62.jpg)

##### 多条管控规则

规则和规则之间是「且」的关系，规则排序越靠前优先级越高，用户可手动拖拽规则排序。

![image](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/WgZOZwaZ3mpelLX8/img/1f4a8da9-b160-48a5-ade0-744a04b1dcd2.jpg)

##### 网段相关的管控条件

若需使用网段相关的管控条件，需要先完成「网段分区」的设置。

![image](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/WgZOZwaZ3mpelLX8/img/710b7293-6d89-408a-8ef9-fcbe300f74f1.jpg)

##### 举例

假设企业管理员需要管控除了项目A和项目B以外的所有项目不允许在「非办公网段」访问，规则设置如下。

![image](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/WgZOZwaZ3mpelLX8/img/f7385560-9b27-4c16-9afa-dce170ee8bc0.jpg)

### 2.0 敏感信息脱敏

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/vBPlNY7zXQLBOdG8/img/d7e570db-1e5d-4ec9-9067-63336cdd9ce0.png)

# 2 网络安全

### 2.1 流量入口安全

域名DNS解析后通过客户的防火墙/SLB 到达TB集群的nginx上，TB集群Nginx只开放443/80端口到外网。

![251beaf6-b424-4d27-b75c-11c540b3f757.svg](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/54Lq3RGKzz9zn7Ed/img/0bcbcf27-e008-4f97-84d4-20004d336cd1.svg)

### 2.2 TLS 1.2以上

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/vBPlNY7zXQLBOdG8/img/784ffdfa-14df-41c0-900d-1b519364319f.png)

# 3 策略性安全

### 3.1 用户cookie过期时间配置

默认过期1年，运维修改全局大values配置文件可修改过期时间

### 3.2 存储

存储加密，敏感数据脱敏存储

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/vBPlNY7zXQLBOdG8/img/5cddfe28-f06e-4527-aa82-cde206bd76b6.png)

### 3.3 数据库备份恢复

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/vBPlNY7zXQLBOdG8/img/88c9e0ef-e894-4674-bb36-65977f19c3c4.png)

### 3.4 运维

操作服务器通过客户的堡垒机/vpn登录。如果没外网到客户现场操作。

### 3.5 三级等保

[请至钉钉文档查看附件《FY25\_LISS2024DJCP0206\_Teambition\_测评报告v3.pdf》。](https://alidocs.dingtalk.com/i/nodes/gwva2dxOW4vRkd9DU5qG4ekdJbkz3BRL?doc_type=wiki_doc&iframeQuery=anchorId%3DX02m2a4ztv2clo3buyb2rd)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/vBPlNY7zXQLBOdG8/img/95bf0712-32f9-4201-9290-3986d2b14722.png)

### 3.6 镜像漏洞扫描

每次TB私有部署包前对TB镜像仓库中的所有镜像发起扫描。[《镜像漏洞扫描流程》](https://alidocs.dingtalk.com/i/nodes/qnYMoO1rWxrkmoj2IZZnN7X1J47Z3je9)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/5VLqXZeNvNZalX19/img/46d617ee-595b-49bb-95ea-ae4b6c7f6158.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/5VLqXZeNvNZalX19/img/a0a9a370-b298-4dc6-980e-41cc8ba9baec.png)

### 3.7 静态代码扫描

[请至钉钉文档查看附件《Teambition静态源代码扫描报告（2024-8月）（盖章）.pdf》。](https://alidocs.dingtalk.com/i/nodes/gwva2dxOW4vRkd9DU5qG4ekdJbkz3BRL?doc_type=wiki_doc&iframeQuery=anchorId%3DX02m2a4ztv68dg23tublq)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/vBPlNY7zXQLBOdG8/img/0ce6c83e-a5f8-4126-89b1-5d4f8fe996a2.png)

### 3.8 渗透测试

阿里安全专家不定期对TB进行渗透测试。[《不定期渗透测试流程》](https://alidocs.dingtalk.com/i/nodes/QG53mjyd800agdlKH5dNRqy986zbX04v)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/Mp7ldGZ8zPbvOBQN/img/aeb44a71-1ecf-474e-bd87-19cf9ce36a26.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/Mp7ldGZ8zPbvOBQN/img/8d1be5e4-d1a8-4c1d-9ad1-4a4edd879a30.png)

### 3.9 信创

信创浏览器：华为浏览器、360安全浏览器、奇安信、QQ浏览器 [《支持的信创浏览器》](https://alidocs.dingtalk.com/i/nodes/NZQYprEoWoxKPoqwCzdqB1XeV1waOeDk)

信创芯片：鲲鹏、飞腾、倚天、海光x86 [《信创支持的操作系统+芯片+数据库+浏览器》](https://alidocs.dingtalk.com/i/nodes/r1R7q3QmWew5lo02fLmNd7aYJxkXOEP2)

信创操作系统：麒麟v10、龙蜥、欧拉 [《信创支持的操作系统+芯片+数据库+浏览器》](https://alidocs.dingtalk.com/i/nodes/r1R7q3QmWew5lo02fLmNd7aYJxkXOEP2)