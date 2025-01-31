# autoxcustomer_allmwork
- 基于LLM模型实现自动客服对接的业务框架，构建智能客服,自动交友/问候/对话,兼容GPT4/Kimi，适配微信/Telegram/TikTok/淘宝

## 业务内测申请

- 本项目中发布的最小环境本地内含设计性缺陷，就是故意删除了核心配置和代码。有经验的朋友如果修补成功，如果想测试本项目的可以向a3coin.org申请业务内测 申请方式：support@a3coin.org⁠

- a3coin.org基金会是一个超级垂类项目，所有的技术都用于对btc业务的开发上。

## 更新日志： 2025-01-31 本项目已经演变成一个超级垂类项目，由a3coin.org基金会提供5个btc支持。

- 1、工作路径的建立，考虑到将本增效的问题，本项目通过单一工作区解耦成用户端--中间工作区--后端小模型--openai接口层--第三方大模型--ai搜索引擎几个结构，每个结构都由独立的容器部署支持快速集群化业务。 用户前端可以支持主流的任意开源aiwebui，只需要能够对接openai接口层即可。
- 2、中间工作区--后端小模型，共同组建了企业自己的小模型库，分成cuad模式对接N卡，推荐是由40604或者40608组成的显卡集群进行数据关联训练；云算力模式对接外部算力
- 3、什么是中间工作区？中间工作区，是指由用户前端采用聊天的方式询问以后，通过小模型库或者大模型接口获取到的答案由前端用户采纳以后缓存的话术关系链表，保存在数据库中由于调用。这个业务的存在是完全处于降本增效的原因（因为大模型api调用太费钱了）
- 4、新版本的业务流程：超级垂类用户通过各种前端向autoxcustome进行问询。autoxcustome首先查询中间工作区如果没有命中数据，则并发的发出两个查询，a发向企业小模型，b发向外部大模型，当收到答复以后，本地会先做语义分析。如果小模型和大模型都有回复，怎对两个回复进行合并处理然后返回给超级垂类用户。如果只由大模型回复，则直接返回回复给超级用户并暂存在中间工作区，再把回复导入小模型
- 5、后端大模型的变更，在以前的版本中我们的后端大模型接口分别使用了gpt、kimi、llm。在本次更新的版本中我们大模型对接的是deepseek（超级便宜）、kimi、llm


## 更新日志：
2024-11-19 更新TG插件，支持群组记账、查u地址监控、群组关键词监控，进群欢迎及问题验证、定时发贴、主动私聊群员，可利用自身LLM私有话术库进行主动客服回访等业务


商务用户请访问我方docker私有库进行拉库更新。拉库账号无权限请联系自己的BD商务查询是否有购买插件权限。在线客请联系群组https://t.me/+BqvlH6BDOWE3NjQ1

通过LLM接口,构建智能客服,自动交友/问候/对话。
## 关于cn后缀的Tag
具有cn后缀的镜像，使用的是阿里的镜像源地址，可以直接引用使用，无需再换源

## 构建方式-构建base镜像
- 1、拉取 redis-v7.0.15 标签镜像，作为基础镜像，主要是为了运行redis环境避免使用远程访问模式。
```
# 国外请用这个命令拉取基础镜像
docker pull aspnmy/autoxcustomer_allmwork:latest

# 国内请用下面这个命令拉取基础镜像
docker pull aspnmy/autoxcustomer_allmwork:redis-v7.0.15-cn
  ```
上面的是基础镜像，不是业务主体，业务主体需要联系你的商务BD进行购买，然后在我方私有库中获得拉取代码的账号及密钥，才能进行拉库更新代码。

- 2、登陆私有库，拉取autoxcustomer_allmwork的最新版本代码包，截至当前时间版本号是v1.21.6
- 3、修改拉取的dockerfile文件中对应的参数，具体修改方式查看帮助文件
- 4、构建autoxcustomer_allmwork base镜像

## 构建方式-构建组件镜像
### 0、服务器硬件要求:
- 4cpu 4g内存 硬盘空间大于1tb为最佳 带宽5mb够用了
### 1、业务正常使用过程中需要使用两个数据库，可以按照下方教程单独构建独立的数据库容器(推荐独立容器)
```dockerfile
name: AutoXcustomer_DB 
services:
    mongo:
        container_name: AutoXcustomer_MongoDB
        ports:
            - 27017:27017
        environment:
            - MONGO_INITDB_ROOT_USERNAME=root
            - MONGO_INITDB_ROOT_PASSWORD=root@#1314
        image: mongo:4.4.0

    pgvector:
        container_name: AutoXcustomer_PostgreSQL
        ports:
            - 5432:5432
        environment:
            - POSTGRES_PASSWORD=root@#1314
            - POSTGRES_USER=admin
        image: ankane/pgvector

```
### 2、常见问题：
如果使用vps，云主机，虚拟机，arm设备，或者内存小于4gb的硬件，使用mongo:4.4.0版本，不要使用最新版本。

### 测试运行
1、确定组件容器全部运行成功的情况下，重新启动base镜像容器，以http://IP:3000的形式进行访问，等待1-5分钟完成部署。
2、对接PayPal交易组件，请参考代码包docs下的教程自行对接
3、对接udst钱包，可选对接，请参考代码包docs下的udst机器人教程自行对接

### 部署私有模型
1、默认代码为缓存模型模式，即通过后端模型接口聊天训练的情况下，本地会缓存训练文件，形成自己的私有模型数据
2、如需对接自己的显卡进行离线训练，请参考oneapi 和 llm_webui的部署方式，先完成一个接口化访问的业务流程，再和本组件对接。

### 对接IM组件
- 1、微信机器人对接教程，请在docs下查找
- 2、Telegram/TikTok/淘宝/WhatsApp等其他IM的对接请联系群组https://t.me/+BqvlH6BDOWE3NjQ1

