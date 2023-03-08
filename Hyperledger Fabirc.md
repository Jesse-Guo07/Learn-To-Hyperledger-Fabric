### 名词回顾

##### Hyperledger Fabric

第一个孵化出来的商用DLT框架

##### DLT

distribute ledger technology，分布式存储的是账本：book of records，DLT问题：隐私/机密/标准化，传统区块链无法解决

##### Hyperledger composer

DLT上创建商业应用的工具

##### 关键组件

peer节点、ordering service排序服务、ca证书中心、msp会员管理提供者；

使用nodejs编写链码（智能合约）

资产assert代表的是价值，资产变更会生成转账transaction（或者叫做事务）

### 重建信任

hyperledger是区块链技术，但不是数字货币，没有挖矿的概念。但其可以创造数字货币

### 数据完整性和安全性

![image-20230306095127150](assets/image-20230306095127150.png)

账本由密码学签名保证

permissioned network： 授权网络 access control

confidential transaction 交易安全 transaction是可以控制可见性的

> 可以创建私有channel

![image-20230306095433661](assets/image-20230306095433661.png)

no cryptocurrency 无数字货币：不用分析51%攻击？

> 没有矿工，低成本，验证操作灵活
>
> ​	programmable 可编程
>
> ​	chaincode 智能合约 链码（在hyperledger特有名词）

可以创建公有链，私有链，联盟链

每个共识协议的参与者都是由CA来认证

### Assert，chaincode&ledger

assert：有价值的东西，可以被交易的东西

> json表示；
>
> 二进制表示

chaincode

> transaction | business logic业务逻辑

ledger

> 记录所有的transaction

所有参与者都保存了ledger

hyperledger相当于存有“两套账本”：交易信息和世界状态

code is law

背书策略：提供一种规则，例如：几个参与者同意后可以出发智能合约

java开销太大，go编写比较麻烦，官方推荐nodejs

### 授权网络和MSP和CA

业务参与要求整个网络的参与者是认识的，实名制的（认证）

MSP：Membership Service Provider：Roles & Access Restrictions

#### 授权网络：修改网络必须由数字签名（x.509标准）

**<u>fabirc ca</u>**是一个高质量的工具，帮助我们生成证书，ca可以为不同用户生成不同的证书，fabric ca支持链式继承，参与者无法抵赖。

MSP是一个接口/规范，可拔插，支持各种认证体系结构，为membership orchestration architecture提供抽象层：提供具体的身份格式、用户证书验证、用户证书撤销、签名生成和验证，Fabric-CA用于生成证书和密钥，以真正的初始化MSP。

fabric-ca是用于身份管理的MSP接口的默认实现。

MSP定义你是谁，你在哪个网络，msp的证书由f-ca来颁发，每个peer都需要msp的证书。MSPID是一个名字定义一组证书，例如某个机构的某一个成员都需要一个mspid，使用hyperledger fabric sdk的时候经常就需要指定mspid。

### Node：peer、client&orderer

node是区块链的通讯终端，以太坊中node都是相同的，在hyperledger中node分为三种：

- client：实例化transaction的（cli 命令行，node sdk：nodejs，java sdk）
- peer：用来存储和同步ledger的数据
- orderer：用来排序分发transaction，类似于矿工

![image-20230307171120951](assets/image-20230307171120951.png)

排序很重要，解决双花问题（并发），创建区块，对区块签名和验证。

### Channel

每个channel可以理解为独立的HF的实例，所有channel是完全独立的，或者理解为一个子网？

不同channel之间不会交换数据（群聊）

一个channel不依赖其他channel

peer默认什么事情都不做，peer要加入到channel之后才能有blockchain，update ledger

sdk有个工具configtx来让peer加入channel、configtxlator修改channel配置

### Chaincode：nodejs/java/go

智能合约，是应用程序

用来更新账本数据，且只能用chaincode

sdk发起一个transaction（client），peer执行chaincode

chaincode必须属于某一个channel，ledger属于某个channel，chaincode操作的是ledger：当你执行一个操作的时候你需要出示你的权限（ca）

chaincode需要在channel中的每个peer安装，否则会导致peer数据不一致，从而对某些错误peer进行踢出

#### chaincode生命周期

chaincode需要先安装，然后必须实例化，实例化chaincode会启动docker容器，在容器里运行chaincode：

- 安装install

- 实例化init

  在hf里chaincode都是在docker容器里运行

- 调用invoke

#### chaincode背书策略

实例化chaincode需要指定背书策略

![image-20230307180303352](assets/image-20230307180303352.png)

### Hyperledger工作流程

![image-20230307180602890](assets/image-20230307180602890.png)



> orderer:solo排序算法-------> kafka（实际使用）------> SBFT  PBFT
>
>  提案-背书-更新申请-调用/更新
>
> 背书成功，验证通过，数据一致才会更新账本数据

![image-20230307182716322](assets/image-20230307182716322.png)

### 术语回顾

![image-20230307182959476](assets/image-20230307182959476.png)

### Docker

[Docker 教程 | 菜鸟教程 (runoob.com)](https://www.runoob.com/docker/docker-tutorial.html)

##### 思想

标准化、模块化

安全性：隔离

##### 解决问题

- devops
- 易扩展
- 弹性计算
- 限制CPU资源

##### 原理

标准化：build、ship、run

1. 构建镜像发布到仓库；
2. 去仓库拉镜像到本地；
3. 把本地的镜像运行起来，运行在容器中

#### 仓库镜像

##### 联合分层文件系统

![img](assets/20190620222324359.jpg)

自上而下发现

Container以下只需要启动一次

#### 容器

其实就是一个虚拟进程

#### 仓库

hub.docker.com

#### docker安装

参考阿里云镜像资源对应的安装教程：[docker-ce镜像_docker-ce下载地址_docker-ce安装教程-阿里巴巴开源镜像站 (aliyun.com)](https://developer.aliyun.com/mirror/docker-ce?spm=a2c6h.13651102.0.0.57e31b11I1BQed)

使用docker拉去nginx镜像：`docker pull nginx`，默认为latest版本

运行nginx：`docker run -p 主机端口:docker端口 -d(后台运行) --name name(docker命名) nginx`

### Hyperledger环境搭建

官方文档：[Using the Fabric test network — hyperledger-fabricdocs main documentation](https://hyperledger-fabric.readthedocs.io/en/latest/test_network.html)

在docker中安装，必要内容下载

下载fabric-samples：`curl -sSL https://raw.githubusercontent.com/hyperledger/fabric/main/scripts/bootstrap.sh| bash -s`

> 采用官方脚本下载，速度很慢，因为有wall：下载内容包括二进制文件 fabric binaries（国内服务器1h24min，很晕）