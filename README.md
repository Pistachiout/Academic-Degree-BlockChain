## 基于Hyperledger Fabric的学位学历认证管理系统
1. `config.yaml`配置文件：描述组织结构、通道结构、所有配置文件、证书的路径
2. `chaincode`：链码文件所在的目录
	- `vendor`：依赖包
	- `edu.go`：链码（智能合约）
3. `fixtures`：fabric基础网络所有文件所在的目录
4. `img`：存放前端的图片
5. `sdkInit`：sdk核心代码
	- `sdkSetting.go`：实现了包括Setup、CreateAndJoinChannel、CreateCCLifecycle等基础功能的函数
	- `integration.go`：实现了包括DiscoverLocalPeers、InitService等基础功能的函数
	- `sdkInfo.go`：定义了一些组织和SDK中用到的结构体
	- `set/get.go`：复制调用链码的两个函数
6. `service`：服务端代码，封装了合约接口的调用
	- `eduService.go`：调用链码的关键所在
7. `explorer`：区块链浏览器文件及配置

项目功能介绍:https://blog.csdn.net/qq_45808700/article/details/129423030

Hyperledger Fabric实践文档：https://learnblockchain.cn/books/enterprise/chapter4_00.html
### 1.部署环境

•Linux Ubuntu（root 用户）

•Golang 语言 ：建议使用go1.17，版本过高会出现问题

•docker 18.09.7

•docker-compose 1.22.0


### 2.环境配置

#### 2.1安装 docker 以及 docker-compose

使用 docker 可以方便的解决程序依赖的环境问题；并且后续需要使用到的 Hyperledger Fabric 框架官方也提供了相应的 docker 的容器。
安装 docker 命令：

```bash
sudo apt install docker.io
```

验证 docker 成功安装：docker version，结果如图所示：

![docker 安装成功](https://img-blog.csdnimg.cn/2747b94a14f34c9698d8d6a3bef4c100.png)

为了方便管理多个 docker 容器，还需要安装 docker-compose：
```bash
sudo apt install docker- compose
```
验证安装成功：docker-compose version，如图所示：



![docker-compose 安装成功](https://img-blog.csdnimg.cn/ffb878a022a949fe89d388a0ad8127ca.png)

#### 2.2安装 golang

区块链框架Hyperledger Fabric 目前支持Java、Go 等主流编程语言并提供了相应的SDK，但是支持最全面的还是 Golang，因此采用 Go 语言来进行开发是比较好的选择；

 1. 安装 Golang：wget https://golang.google.cn/dl/go1.17.linux-amd64.tar.gz
 下载受网络环境影响，如果您本地有相应的 tar 包，则直接解压到指定的路径下即可。
 3. 使用 tar 命令将压缩文件解压至指定路径/usr/local/下： tar -zxvf go1.17.0.linux-amd64.tar.gz -C /usr/local
 4. sudo -s使当前用户为 root 用户，$HOME=/root
 5. sudo vim /etc/hosts,添加：
 
```bash
127.0.0.1  orderer.example.com
127.0.0.1  peer0.org1.example.com
127.0.0.1  peer1.org1.example.com
```


 5. sudo vim /root/.bashrc文件,添加环境变量 GOHOME 以及 GOROOT：  

```bash
export GOPATH=/root/go
export GOROOT=/usr/local/go
export PATH=$GOROOT/bin:$PATH
```

 6. 激活环境变量： source /etc/profile
 7. 更新网络：service network-manager restart
 8. 验证安装成功，使用 go version 结果如图所示：

![Golang 安装成功](https://img-blog.csdnimg.cn/d449c551a6d449d7a640752149be939c.png)
### 3.项目部署

 1. 1.创建保存项目的文件夹： mkdir -p $GOPATH/src
 2. 进入文件夹： cd $GOPATH/src
 3. 从 github 仓库克隆项目到education文件夹： git clone https://github.com/Pistachiout/Academic-Degree-BlockChain.git education
 4. 进入项目目录：cd education
 5. 添加项目开发需要依赖的 Golang 包：go mod tidy
命令可能会执行失败，此时设置代理即可：go env -w GOPROXY=https://goproxy.cn


### 4.启动项目

由于每次启动流程相对固定，首先进入root用户，并配置环境，然后启动项目在项目的目录下运行 clean_docker.sh 脚本即可启动项目：

```bash
sudo -s
source /root/.bashrc
cd $GOPATH/src/education
export GO111MODULE=on
export GOPROXY=https://goproxy.io
go mod tidy
./clean_docker.sh
```
### 5.启动区块链浏览器
再开一个终端，按以下步骤启动Fabric浏览器（在启动Web项目之后，才能启动区块链浏览器）
```bash
sudo -s
source /root/.bashrc
cd $GOPATH/src/education/explorer
docker-compose -f docker-compose.yaml up -d
```
使用docker-compose down -v关闭浏览器，Ctrl + C 停止Web服务

### 6.项目结果：

项目启动成功的结果如图所示：

![启动项目成功](https://img-blog.csdnimg.cn/7c55555eeed94d9bbacd18cd7d7bb05e.png)


通过浏览器访问 localhost:9000端口即可进入 web 端，结果如图所示：




: ![启动项目成功](https://img-blog.csdnimg.cn/c5a33dd311bd46c792da068a291b3839.png)

通过浏览器访问localhost:8080即可进入 区块链浏览器，结果如图所示：
![image](https://user-images.githubusercontent.com/63298680/231712067-d7cafefe-615c-495c-ba20-92827b10dfc2.png)

CouchDB 数据库1：peer0节点7051端口 `http://localhost:5984/_utils`

CouchDB 数据库2：peer1节点9051端口 `http://localhost:7984/_utils`


![在这里插入图片描述](https://img-blog.csdnimg.cn/22192eb759314993baeb3f72377343c5.png)

管理员账号：admin 123456

普通用户账号密码：bob 123456

浏览器账号密码：exploreradmin  exploreradminpw

有帮助的话希望点亮star收藏一下，如果部署有困难可以加qq2097731053

### 7 .关闭项目
Ctrl + C 停止Web服务，然后使用如下命令清空：
```bash
make clean
```
### 8 .常见部署问题
![image](https://github.com/Pistachiout/Academic-Degree-BlockChain/assets/63298680/88402e0a-9ce4-49a8-aa99-2f1644f33c6d)

上图的问题是网络问题，可以在宿主机下载好go安装包，然后放入虚拟机中解压

![image](https://github.com/Pistachiout/Academic-Degree-BlockChain/assets/63298680/103f59a4-673c-41a5-8fa3-8d66e999dfb2)

若出现上图的问题，说明你的go语言版本过高，可以换为go1.17

Create channel and join error: Create channel error: error should be nil for SaveChannel of orgchannel: create channel failed: create channel failed: SendEnvelope failed: calling orderer 'orderer.example.com:7050' failed: Orderer Server Status Code: (400) BAD_REQUEST. Description: error applying config update to existing channel 'mychannel': error authorizing update: error validating ReadSet: proposed update requires that key [Group] /Channel/Application be at version 0, but it is currently at version 1

若报上述错误，可进入fixtures目录，执行①doucker-compose down -v ②docker volume prune ③docker container rm -f $(docker container ls -aq)

感谢[@zhangrufeng](https://github.com/zhangrufeng)提的issue，详情见https://github.com/Pistachiout/Academic-Degree-BlockChain/issues/1#issuecomment-2126708483
