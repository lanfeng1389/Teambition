# Teambition私有部署概要设计

# 引言

### 目的

本概要设计说明书编写的目的是为了对Teambition软件系统的整体做功能阐述，包了功能架构、数据流向、技术架构等内容。含他提供给研发人员和相关的利益人员一个对系统设计的整体了解。

### 范围

本文档用于软件设计阶段的概要设计，它的上游（依据的基线）是需求分析规格书，它的下游是系统详细设计说明书，并为详细设计说明书提供测试的依据。

软件概要设计的范围是：软件系统的总体结构、外部接口、主要部件功能分配、全局数据结构以及部件之间的接口等方面的内容。

### 背景

### 术语定义

| 术语 |  |
| --- | --- |
| k8s/Kubernetes | 是一个开源的，用于管理云平台中多个主机上的容器化的应用，Kubernetes的目标是让部署容器化的应用简单并且高效（powerful）,Kubernetes提供了应用部署，规划，更新，维护的一种机制。 |
| cronjob | CronJob 用于执行排期操作,例如备份、生成报告等。是一款linux服务。 |
| webhook | 网络钩子是“用户定义的HTTP回调”。 |
| elasticsearch | Elasticsearch 是位于 Elastic Stack 核心的分布式搜索和分析引擎。 |

### 设计规范

1.    变量对象命名规则
    

*   使用有意义的名称：命名应该反映变量的含义和用途，避免使用无意义或[模棱两可](https://so.csdn.net/so/search?q=%E6%A8%A1%E6%A3%B1%E4%B8%A4%E5%8F%AF&spm=1001.2101.3001.7020)的名称。
    
*   使用驼峰式命名法：对于变量名由多个单词组成时，采用驼峰式命名法，即除第一个单词外的每个[单词首字母大写](https://so.csdn.net/so/search?q=%E5%8D%95%E8%AF%8D%E9%A6%96%E5%AD%97%E6%AF%8D%E5%A4%A7%E5%86%99&spm=1001.2101.3001.7020)，其余字母小写，例如 myVariableName。
    
*   避免使用特殊字符和空格：变量名应只包含字母、数字和下划线（\_），不包含空格和特殊字符。
    
*   有意义的缩写：如果变量名过长，可以使用合理的缩写，但要确保缩写是常见且易于理解的。
    
*   一致性：保持变量命名的一致性，使用相似的命名约定，使代码更易于阅读和维护。
    

1.  数据库对象命名规则
    

*   命名建议使用具有意义的英文词汇，词汇中间以下划线分隔。
    
*   按照业务、产品线或者其它指标进行区分，一般不要超过 20 个字符。如：Teambition库 (teambition)、Teambition日志库(teambition-log)。
    
*   命名只能使用英文字母、数字、中短线。
    
*   所有数据库对象使用小写字母。
    
*   同一业务或者模块的表尽可能使用相同的前缀，表名称尽可能表达含义。
    
*   表名称多个单词不进行分割直接拼接，如优先级分组表 (prioritygroups)，不推荐超过 32 个字符。
    

# 总体架构设计

### 总体架构设计

##### 功能架构图

Teambition的基础能力包括了任务类型、任务字段、文件管理、权限管理等，这部分基础能力基本上和业务属性没有关系，是比较纯粹的模块。基于Teambition基础能力，构建了Teambition的核心场景，包含了任务协同、项目协同、甘特图、统计等。Teambition也构建了自己的开放平台，将Teambition的核心能力通过接口和webhook方式提供给生态伙伴和客户使用，通过开放平台，可以打造更多场景。

功能架构图如下图所示：

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/KM7qe9yeYbEkqpj8/img/5b37a361-f6b4-4675-a407-211954d9aebe.png)

##### 集成架构图

Teambition 作为一个项目管理平台，本身有独立的用户/通讯录系统，但考虑到很多私有部署客户已经有自己的用户/通讯录系统，甚至有的客户要求使用客户统一的门户网站进入Teambition，因此Teambition开放平台也承担对接其他业务系统的责任。

1，对接钉钉。Teambition通过与钉钉开放平台进行深度集成，可以达到在钉钉客户端上打开客户自建Teambition的效果。此外，Teambition系统中产生的任务表更消息也通过钉钉开放平台推送到用户的钉钉客户端中，也可以在钉钉文档中直接创建Teambition任务。

2，对接企业微信/飞书。Teambition可以通过对接企业微信/飞书的开放平台，将Teambition嵌入到企业微信/飞书客户端中。Teambition与微信/飞书进行SSO对接，无需使用Teambition账号密码登录，在微信/飞书客户端中直接打开Teambition。

3，对接客户自建系统。Teambition通过与客户自建系统SSO对接，可以在Teambition登录时跳转到客户的自建系统统一登录页面，客户自建系统的通讯录通过同步方式同步到Teambition数据库中，客户自建系统门户网站也可以嵌入Teambition。

![image](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/63o3XVklZfnlvaGE/8c7b3fa15bd647b9b0240a03e580ad6b0521.png)

##### 数据流向图

1，核心业务数据。Teambition的核心业务（项目、任务、通讯录）数据是由用户侧产生。核心业务数据会通过业务服务将业务数据存储到mongodb数据库中，同时，核心服务也会通过业务服务将附件/图片数据存储到远程共享存储上面（比如nfs协议挂载磁盘、nas存储、oss存储）进行永久存储。

2，统计数据。通过使用mongodb的oplog能力，teambition将mongodb中的部分业务数据通过kafka同步到postgresql中，统计业务读取postgresql中的数据展示给客户。

3，高级搜索数据。通过使用mongodb的oplog能力，teambition将mongodb中的部分业务数据通过rocketmq同步到elasticsearch搜索引擎中，高级搜索也通过elasticsearch检索数据。

![image](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/63o3XVklZfnlvaGE/3510e0a603e741c7bdc91dffd3fcc2180521.png)

##### 技术架构

后端框架。Teambition后端服务使用的语言有nodejs、java、golang。各服务之间通过http协议进行通讯。使用的主要框架包括gear、egg、springboot。

| **框架** | **版本** | **说明** |
| --- | --- | --- |
| gear 系列 | v1.21.3 | Teambition 自研 Golang HTTP 服务框架 |
| gin | v1.6.3 | Golang HTTP 开放框架 |
| egg | v2.33.1 | 主服务nodejs HTTP 开放框架 |
| koa | v2.14.2 | 主服务nodejs HTTP 开放框架 |
| express | v4.18.2 | 主服务nodejs HTTP 开放框架 |
| spring-boot | v2.1.0.RELEASE | 报表服务Java 开发框架 |
| pandora-boot | v2.1.15.1 | 阿里巴巴 Java 开发框架 |

前端框架。Teambition前端服务主要使用nodejs进行开发。其中使用到的主要框架和组件包括React、jQuery、express等。

| **框架** | **版本** | **说明** |
| --- | --- | --- |
| React | 16.8 | UI基础框架 |
| jQuery | 2.2 | 前端基础库，旧代码依赖 |
| Backbone | 1.3.3 | MVC框架 |
| @alifd/next | 1.19.25 | UI基础组件库 |
| react-router | 5.2.0 | 前端路由管理 |
| redux | 4.0.0 | 状态管理 |
| lodash | 4.17.21 | 工具集 |
| moment | 2.22.1 | 时间处理工具 |
| immer | 9.0.2 | 不可变数据实现 |
| rxjs | 5.x&6.x | 响应式编程框架 |
| express.js | 4.18.1 | Node.js中轻量级的web框架 |
| worker-dom | 0.1.x | webworker内的DOM API实现 |
| yoga | 1.9.3 | flexbox布局引擎，用于甘特图场景 |
| engine.io-client | 3.4.3 | socketio客户端 |
| slate | 0.76.0 | 富文本编辑器 |
| jsdom | 21.1.1 | 纯js环境的dom实现 |

##### 部署架构图

1.  请求主链路。浏览器 -> SLB -> nginx -> traefik网关 -> k8s service -> pod。
    
2.  各部分解读。底座层是整个架构的基础，其中不仅包含了应用运行环境k8s，也包含了私有镜像仓库docker-registry，底座层负责控制和协调操作系统的各种资源，业务负责应用在集群中的高可用保障。应用层大部分是基于helm安装的k8s 资源（pod，job，configmap, deployment），非边缘服务replica数量均是>=3，且均是无状态服务，容器间通过service name调用。如要数据落地，将容器内部的路径挂载到pvc中，pvc mount到本地磁盘中。应用层中还有少量的二开服务，目前二开服务通过pm2进程进行管理，nginx直接路由到宿主机端口上。master1的应用层还有TES前端和后端程序。流量分发层负责流量分发和负载，目前有两种分发方式：通过nginx直接分发到节点端口上；通过nginx分发到k8s的treafik网关中，再通过treafik网关对路由进行改写和转发，最终转发到应用的service上。
    
3.  高可用。第一层，slb做负载；第二层，nginx到gateway 做负载，优先到本机，另外两台backup ； 第三层，k8s service做负载；第四层，所有中间件均使用副本集模式，同时存储3份数据。
    

![image](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/63o3XVklZfnlvaGE/a01faf7015c04ea0ba5b9a2ccbf41fd40521.png)

![image](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/63o3XVklZfnlvaGE/53e652208d4f4eadb4249e190bb615070521.png)

### 运行环境设计

##### 硬件平台

服务器最低配置要求：最低要求是1台服务器，生产环境必须是3台服务器。推荐的做法是，测试环境使用1台服务器，不适用额外的数据存储盘；生产环境使用3台服务器，3台服务器可以组成一个集群，另外再挂载一块额外的存储盘用于存储文件数据。以下是单台服务器的硬件配置。

| **资源名称** | **描述** |
| --- | --- |
| CPU | \>= 16核 |
| 内存 | \>= 64GB |
| 本地磁盘 | \>= 500GB （/ 根分区）SSD  HDD |
| 国产系统磁盘要求 | \>= 1TB （/根分区） |
| 数据存储 | 可选 （/teambition/data/） |
| 服务器权限 | root权限 |
| 服务器网络 | 能否访问internet |
| 端口 | 除业务端口外，需要开通18880端口部署使用，可参考(80/443/18880)等 |
| 显示器/键盘/鼠标 | 使用外设通过浏览器访问Teambition部署系统 |

##### 软件平台

操作系统支持Centos7.9/Redhat8.8/麒麟V10(sp2)，如果是集群部署，3台服务器的操作系统版本要一致。另外域名和证书可根据实际情况提供。如果需要将Teambition和钉钉集成，服务器要能访问公网，具体方案参考以下表格。

| 资源名称 | 描述 |
| --- | --- |
| 操作系统 | CentOS 7.9 （推荐）<br>Redhat 8.8<br>Anolis 7.9 / 8.8<br>国产麒麟V10(SP2) |
| 业务访问方式 | IP 或者 域名 \*  2 |
| 业务访问协议 | HTTP 或者 HTTPS |
| 端口 | 业务端口、文件服务端口 |
| CA证书 | HTTPS协议需要配置CA证书，需要商业版 |
| **钉钉集成要求，TB 须放到公网，必须使用域名 + 证书方式访问。** |  |
| 方案1： 服务器直接可以访问外网<br>方案2:    服务器出外网可以加白，加白地址：<br>1: [https://api.dingtalk.com](https://api.dingtalk.com/)[https://api.dingtalk.com](https://api.dingtalk.com/) ------ 集成钉钉内容 钉钉开放接口，比如 项目群群模板接口、工作通知、钉钉待办、钉钉文档<br>2: [https://oapi.dingtalk.com](https://oapi.dingtalk.com/) ------ 集成钉钉内容使用 钉钉开放接口，比如 通讯录接口、企业信息接口 等<br>3: [https://tb.dingtalk.com](https://tb.dingtalk.com/)[https://tb.dingtalk.com](https://tb.dingtalk.com/) 调钉钉接口的，比如发送动态卡片、钉钉审批、钉盘、项目群 等<br>4: [http://MQ\_INST\_1328987352369928\_BX814T9L.mq-internet-access.mq-internet.aliyuncs.com:80](http://MQ_INST_1328987352369928_BX814T9L.mq-internet-access.mq-internet.aliyuncs.com:80) 云上RocketMQ，代理接收弹内MetaQ事件<br>5: https://wss-open-connection.dingtalk.com:443 钉钉stream自动同步地址 |  |

# 模块设计

##### 模块列表及描述

| 模块名称 | 描述 |
| --- | --- |
| 任务模块 | 任务管理，任务类型管理，任务状态管理 |
| 通讯录模块 | 包含用户、组织架构、企业 |
| 认证模块 | 通用的认证模块，可用于登录、开放平台认证 |
| 消息模块 | 内部统一消息监听和分发 |
| 数据同步模块 | 统一的数据变更监听和分发 |
| 文件模块 | 统一的文件上传下载，可对接磁盘、PV、OSS |
| 登录模块 | 统一登录模块，可对接其他SSO系统 |
| 高级搜索模块 | 统一的全文搜索模块 |
| 项目管理 | 项目管理、项目集管理、项目权限管理 |
| 工时管理 | 工时业务 |
| 测试用例管理 | 测试用例业务 |
| 企业管理后台 | 企业管理后台，管理通讯录、优先级、安全等 |
| 开放平台 | 提供对外的接口和webhook能力 |
| 任务捷径 | 任务聚合筛选查看业务 |
| 日程 | 日程业务 |
| 统计 | 统计业务，多维度多视角对项目、任务等数据进行统计分析 |

### 模块关系

公用模块分为公用业务模块和公用基础组件模块，其中公用业务模块可用给不同的专用模块调用，公用基础组件模块既可以让专用模块调用，也可以让公用业务模块调用。

消息模块负责前后端websocket通讯。数据同步模块负责通过oplog获取mongodb主数据。文件模块负责存储文件。

高级搜索模块负责将业务数据存储到elasticsearch中，并创建索引。通讯录模块存储用、部门、企业信息。认证模块负责用户、应用认证。登录模块负责账号密码登录、三方SSO登录。

![image](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/63o3XVklZfnlvaGE/ee8e4908e0c048b586d2f32cdf29bcbf0521.png)

# 数据库设计

### 4.1数据库名清单

| 数据库名称 | 描述 |
| --- | --- |
| teambition | 核心数据库，包含项目数据、任务数据、用户数据、工时数据等 |
| dev-center | 开放平台相关数据，包含应用数据、扩展数据、webook配置等 |
| worktime | 工时业务相关数据 |
| testhub | 测试用例业务相关数据 |
| autoflow | 工作流业务相关数据 |
| teambition-log | 核心审计日志数据 |

**4.2 数据库表关系说明**

| **表名** | **数据库** | **描述** | **其他表关联** |
| --- | --- | --- | --- |
| tasks | teambition | 任务表 | projects、users、tasklist |
| projects | teambition | 项目表 | organizations、users |
| members | teambition | 成员表 | users、organizations |
| tasklists | teambition | 任务列表 | users、projects |
| users | teambition | 用户表 |  |
| app\_extensions | dev-center | 应用扩展表示 | users、app\_versions |
| app\_settings | dev-center | 应用配置表 | app\_versions |
| app\_versions | dev-center | 应用表 | organizations、users |
| worktimes | worktime | 工时表 | users、organizations |
| testhubs | testhub | 测试用例表 | organizations、users |
| flow | autoflow | 工作流表 | users |
| coreactionlogs | teambition-log | 审计日志表 | organizations、users |

# 接口设计

**接口设计规范**

开放接口设计总体遵循RESTful API规范。

操作应该使用 HTTP 动词来表达，例如 GET、POST、PUT、DELETE 等，以确保对资源的操作被明确表示和限制。

应该使用名词来表示资源，而不是动词，以避免歧义和混淆。

应该使用查询参数来过滤和分页资源。使用 JSON 来表示数据，以便不同的客户端能够方便地进行数据解析和处理。

应该使用版本号来管理 API 的不同版本，以便支持旧版 API 的兼容性和平稳升级。

### 接口认证

API 请求时需要使用 AppId 和 AppSecret 生成 app\_access\_token, 然后携带在请求 header 的 Authorization 中, Teambition 会根据 app\_access\_token 校验开发者是否有权限调用企业内的 API 权限。

### 数据隔离

企业 ID 是企业在 Teambition 中的唯一标识,每个企业拥有唯一的企业 ID, 企业 ID 可以在 Teambition 企业管理后台中查看。调用接口时，可以传入企业id或者X-Tenant-Id 

### 接口限流

每个接口标准限流为40qps

### 统一错误码

```ini
00000 GatewayError 网关级错误
00001 AppDisabled 应用在该企业被停用
00002 AppUninstalled 应用在该企业已被卸载
00003 AppNotInstalled 应用在该企业未安装或该应用不存在
00004 TokenInvalid 应用凭证错误，请检查凭证是否正确
00005 NoPermission 没有调用该API的权限
10000 ServiceError 服务级错误
10001 AccessTokenMissing 缺少 access_token，请检查 access_token
10002 ParamError 参数有误
10003 MissingRequiredField 操作失败, 缺少必填项
10004 NotFound 未找到资源
10005 NoPermission 无权限操作资源，访问被拒绝
10006 RequiredError xx 字段是必须的
10007 InvalidTenantOperator 操作人不属于租户
10008 TaskArchived 任务已移入回收站
10009 InvalidTenantResource 租户资源不属于当前租户，请检查租户ID是否输入正确
10010 AncestorTaskArchived 父任务已移入回收站
10011 AncestorTaskDone 父任务已经完成
10012 ApplicationNotEnabled xx 插件未开启
10013 AtLeastOneStatus 至少需要1个开始状态
10014 AutoLinkNotSupportSfc 自动关联不支持关联任务的任务类型
10015 HasUndoneTask 存在未完成的子任务
10016 NoTaskflowstatusPermission 无权限变更到该任务状态
10017 UploadLimitExceeded 文件超过上限，请升级版本
10018 StartDateCanNotAfterDueDate 开始时间必须早于截止时间
10019 DueDateCanNotBeforeStartDate 截止时间必须晚于开始时间
10020 CanNotMoveToTheTaskflowstatus 不能流转到该任务流状态
10021 CanNotLinkSelf 不可关联自身
10022 hasUnStartDependencyCannotBeStart 前置任务还未开始，无法开始当前任务
10023 hasUndoneDependencyCannotBeStartOrDone 前置任务未完成，无法开始或者完成当前任务
10024 NotFoundError.work 未找到 work 资源
10025 InvalidPageToken 无效的 PageToken
10026 TaskIsInCompletedSprint 任务所属迭代已完成，重新打开迭代才能变更状态
```

### 开放接口清单

参考： [https://open.teambition.com/docs/documents/66cd468fff36bd665cccf040](https://open.teambition.com/docs/documents/66cd468fff36bd665cccf040)

# 可维护性和稳定性设计

### 管理员管理用户

提供用户管理功能，比如用户禁用，用户密码修改，重置密码。管理员用户通过运维在配置文件中指定。用户禁用，当管理员禁用用户后，被禁用的用户不能通过系统登录页登录，也不能访问任何接口。用户密码修改，修改用户密码需要有密码强弱规则，字符串要大于8个，至少包含大写小写和数字。重置密码，管理员重置密码成功后，用户通过登录页登录，会提示强制修改密码，密码修改成功后方可进入系统。

### 容灾设计

Teambition系统使用的数据库是Mongodb、postgresql数据库，这两种数据库均做了副本集模式，数据会存储在3台服务器中，只要不是3台服务器磁盘同时损坏，数据就能恢复。同时，通过cronjob每天触发一次全量备份，全量备份的数据库数据放到共享存储磁盘中。

### 日志设计

Teambition系统运行在k8s集群中，k8s集群中部署了loki服务，通过exporter监听k8s服务的日志，并将日志存储在loki服务中。由于服务包含了job和pod， 均有运行状态，loki中存储的业务日志既包含了job的日志，也包含了pod的日志。

k8s集群中也部署了grafana服务，grafana通过对接loki后，用户可以在grafana中全文搜索loki中的日志。日志的格式至少包含时间、请求方ip、日志主体、接口请求耗时。

### 监控告警设计

在k8s集群中部署prometheus， prometheus是一个监控服务，可以监控k8s集群中的宿主机信息、k8s集群信息、k8s服务等。监控的宿主机应包含CPU、内存、磁盘容量、磁盘IO、网络IO等。k8s集群监控应包含pod资源监控、k8s集群状态监控等。k8s服务监控应包含业务服务资源情况、接口请求超时、接口请求失败等。

触发的告警可以通过两种方式发送出来。告警默认是发送到钉钉群中，客户可以配置自己的告警群。第二种方式是配置客户的任意接口回调地址，对接客户自己的监控平台。