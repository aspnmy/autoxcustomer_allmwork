# autoxcustomer_allmwork
基于LLM模型实现自动客服对接的业务框架，构建智能客服,自动交友/问候/对话,兼容GPT4/Kimi，适配微信/Telegram/TikTok/淘宝

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

