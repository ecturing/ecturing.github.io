---
title: Docker
date: 2022-03-15 22:22:05
categories:
- 框架
tags:
- docker
- 后端
- 容器化
---
Docker 是一个开源的应用容器引擎，是一种容器虚拟化技术，基于 Go 并遵从 Apache2.0 协议开源。Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。容器是完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 app）,更重要的是容器性能开销极低。

虚拟机也是虚拟化技术的一种体现，但是虚拟机占用资源多，冗余步骤多，启动慢，容器技术并不是模拟一个完整的操作系统，而是对进程进行隔离，用容器将软件所需的环境打包到一个隔离的容器中，容器只需要软件工作所需的库资源的设置

![](https://d33wubrfki0l68.cloudfront.net/26a177ede4d7b032362289c6fccd448fc4a91174/eb693/images/docs/container_evolution.svg)

## Docker安装

### Docker组成

![Untitled](https://www.runoob.com/wp-content/uploads/2016/04/576507-docker1.png)

| Client | 客户端 |  |
| --- | --- | --- |
| Host | 服务端 |  |
| Registry | 仓库 | 集中存放镜像文件的场所 |
| Images | 镜像 | 镜像是一个只读的模板，用于创建容器，类比面向对象，镜像是类 |
| Containers | 容器 | 容器是镜像的一个实例，类比面向对象，容器是对象 |
| Docker daemon | Docker守护进程 | docker容器以后台形式运行 |

### Docker命令

### Docker 安装

```bash
yum install -y yum-utils device-mapper-persistent-data lvm2
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
yum install docker-ce docker-ce-cli containerd.io
```

### 启动Docker

```bash
systemctl start docker
```

### 测试命令

```bash
docker version 
docker run hello-world
docker images
```

配置镜像加速（阿里云）

```bash
cd /etc/docker/
vim daemon.json
```

加入以下代码

```json
{
"registry-mirrors": ["https://xxxxx.mirror.aliyuncs.com"]
}
```

### 常用命令

#### 镜像命令

```bash
docker images
#可选
-a： 列出本地所有镜像
-q： 只显示镜像id
--digests： 显示镜像的摘要信息
```

```bash
docker search
#可选项
--filter=stars=50 ： 列出收藏数不小于指定值的镜像。
```

```bash
docker pull
#可选
tag 指定镜像版本，不填默认最新，latest
```

```bash
docker rmi
#删除镜像
docker rmi -f 镜像id # 删除单个
docker rmi -f 镜像名:tag 镜像名:tag # 删除多个
docker rmi -f $(docker images -qa) # 删除全部
```

#### 容器命令

```bash
# 新建容器并启动命令
docker run [OPTIONS] IMAGE [COMMAND][ARG...]
# 常用参数说明
--name="Name" # 给容器指定一个名字
-d # 后台方式运行容器，并返回容器的id！
-i # 以交互模式运行容器，通过和 -t 一起使用
-t # 给容器重新分配一个终端，通常和 -i 一起使用
-P # 随机端口映射（大写）
-p # 指定端口映射（小结），一般可以有四种写法
```

```bash
#退出容器
exit
```

```bash
#列出所有运行容器命令
docker ps [OPTIONS]
# 常用参数说明
-a # 列出当前所有正在运行的容器 + 历史运行过的容器
-l # 显示最近创建的容器
-n=? # 显示最近n个创建的容器
-q # 静默模式，只显示容器编号。
```

```bash
docker start (容器id or 容器名) # 启动容器
docker restart (容器id or 容器名) # 重启容器
docker stop (容器id or 容器名) # 停止容器
docker kill (容器id or 容器名) # 强制停止容器
```

```bash
docker rm 容器id # 删除指定容器
docker rm -f $(docker ps -a -q) # 删除所有容器
docker ps -a -q|xargs docker rm # 删除所有容器
```



## 容器数据卷

> 将应用和运行的环境打包形成容器运行，运行可以伴随着容器，但是我们对于数据的要求，是希望能够 持久化的！
> 
> 
> 所以我们希望容器之间有可能可以共享数据，Docker容器产生的数据，如果不通过docker commit 生成 新的镜像，使得数据作为镜像的一部分保存下来，那么当容器删除后，数据自然也就没有了！这样是行不通的！
> 为了能保存数据在Docker中我们就可以使用卷！让数据挂载到我们本地！这样数据就不会因为容器删除 而丢失了！
> 

特点：

1、数据卷可在容器之间共享或重用数据 

2、卷中的更改可以直接生效 

3、数据卷中的更改不会包含在镜像的更新中 

4、数据卷的生命周期一直持续到没有容器使用它为止

命令格式

```bash
-v 卷名:/容器内路径 
docker run -d -P --name nginx02 -v nginxconfig:/etc/nginx nginx
```

## Docker File

> Docker File 是一个用来构建镜像的文本文件，文本内容包含了一条条构建镜像所需的指令和说明。

### 常用指令

- `FROM`  基础镜像，当前新镜像是基于哪个镜像的 MAINTAINER # 镜像维护者的姓名混合邮箱地址 

- `RUN` 容器构建时需要运行的命令 

- `EXPOSE`  当前容器对外保留出的端口 

- `WORKDIR`  指定在创建容器后，终端默认登录的进来工作目录，一个落脚点 

- `ENV`#用来在构建镜像过程中设置环境变量

-  `ADD` 将宿主机目录下的文件拷贝进镜像且ADD命令会自动处理URL和解压tar压缩包 

- `COPY`  类似ADD，拷贝文件和目录到镜像中！

- `VOLUME`  容器数据卷，用于数据保存和持久化工作 

- `CMD` 指定一个容器启动时要运行的命令，Docker File中可以有多个CMD指令，但只有最 后一个生效！ 

- `ENTRYPOINT` 指定一个容器启动时要运行的命令！和CMD一样 ONBUILD # 当构建一个被继承的Docker File时运行命令，父镜像在被子镜像继承后，父镜像的 ONBUILD被触发

### 示例

以一个不包含任何其他功能的简单的Spring Boot应用为例子

```dockerfile
FROM java:8-jre #指定jre运行库
ADD WebChat_docker-0.0.1-SNAPSHOT.jar WebChat-0.0.1.jar #拷贝maven打包的jar包到容器目录
EXPOSE 8080 #指定容器的暴露端口为8080
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/WebChat-0.0.1.jar"] #相当于启动容器时执行Java命令启动项目
```

## Docker远程配置

对于经常使用云服务设备或远程开发的人员来说，常常需要远程操控Docker，如使用IDEA进行开发和打包后上传到服务器进行运行，对于内网环境来说直连安全隐患较低，但是对于公网环境的设备直接暴露Docker操作端口可导致被入侵，极其容易被扫描后提权入侵宿主机，docker官方提供了以TLS证书实现的安全加密隧道连接的教程，配置方法如下(操作环境为Linux)

### 使用OpenSSL创建签名文件

```bash
#创建证书文件夹
mkdir cert
#创建密码，连续输入两次
openssl genrsa -aes256 -out ca-key.pem 4096
#依次输入密码、国家、省、市、组织名称等
openssl req -new -x509 -days 365 -key ca-key.pem -sha256 -out ca.pem
#生成server-key.pem
openssl genrsa -out server-key.pem 4096
#生成server.csr（把下面的$HOST换成你自己服务器外网的IP或者域名）
openssl req -subj "/CN=$HOST" -sha256 -new -key server-key.pem -out server.csr
#配置白名单0.0.0.0表示所有ip都可以连接。（这里需要注意，虽然0.0.0.0可以匹配任意，但是仍需要配置你的外网ip和127.0.0.1，否则客户端会连接不上）
echo subjectAltName = IP:0.0.0.0,IP:123.123.123.123,IP:127.0.0.1 >> extfile.cnf
#将Docker守护程序密钥的扩展使用属性设置为仅用于服务器身份验证
echo extendedKeyUsage = serverAuth >> extfile.cnf
#输入之前设置的密码，生成签名证书
openssl x509 -req -days 365 -sha256 -in server.csr -CA ca.pem -CAkey ca-key.pem \
  -CAcreateserial -out server-cert.pem -extfile extfile.cnf
#生成供客户端发起远程访问时使用的key.pem
openssl genrsa -out key.pem 4096
#生成client.csr（把下面的IP换成你自己服务器外网的IP或者域名）
openssl req -subj "/CN=$HOST" -new -key key.pem -out client.csr
#创建扩展配置文件，把密钥设置为客户端身份验证用
echo extendedKeyUsage = clientAuth > extfile-client.cnf
#生成cert.pem，输入前面设置的密码，生成签名证书
openssl x509 -req -days 365 -sha256 -in client.csr -CA ca.pem -CAkey ca-key.pem -CAcreateserial -out cert.pem -extfile extfile-client.cnf
#删除不需要的配置文件和两个证书的签名请求
rm -v client.csr server.csr extfile.cnf extfile-client.cnf
#为了防止私钥和公钥文件被更改以及被其他用户查看，修改其权限为所有者只读
chmod -v 0400 ca-key.pem key.pem server-key.pem
chmod -v 0444 ca.pem server-cert.pem cert.pem
```

### 更改docker配置配置开启仅TLS连接并指定证书位置

```bash
cd /usr/lib/systemd/system
vim docker.service
```

找到

```bash
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
```

添加一个空格加 \，然后回车输入以下内容($PWD根据实际目录进行修改，cert对应第一步创建的certs文件夹)

```bash
--tlsverify \
--tlscacert=$PWD/certs/ca.pem \
--tlscert=$PWD/certs/server-cert.pem \
--tlskey=$PWD/certs/server-key.pem \
-H tcp://0.0.0.0:2376 \
-H unix:///var/run/docker.sock \
```

最后重新启动docker

```bash
systemctl daemon-reload
systemctl restart docker
```

保存证书密钥

保存三个密钥ca.pem，cert.pem，key.pem到客户机，保存到一个空目录下
