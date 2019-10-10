---
title: linux与docker实践
date: 2019-09-22
categories: linux
author: yangpei
tags:
  - linux
  - docker
comments: true
cover_picture: /images/banner.jpg
---

**devOps流程有哪些？**
DevOps要求开发、测试、运维一体化，实现敏捷开发、敏捷部署和敏捷生产的目标。包括plan（计划）、code（编码）、build（构建）、test（测试）、release（发布）、deploy（部署）、operate（运营）、monitor（监控）这几大流程。DevOps就是把敏捷开发部门和运维部门之间的围墙打通，形成闭环。

<!-- more -->

计划——编码——构建——测试——发布——部署——运营——监控
<img src="https://i.loli.net/2019/09/18/DFfQdUReJAcuTHK.jpg" width="50%" alt="devOps流程" />
[图片参考https://blog.csdn.net/gcttong00/article/details/82892676]
**自己之前没有了解的流程有哪些？举一个自己开发的项目的例子，来描述devOps流程，思考怎样才能效率开发？**

在腾讯实习过程中，了解了部门项目开发流程：需求-视觉设计-需求评审-开发排期-开发与接口联调-测试-上线。每个新功能（feature）的提出必须排期，跟进团队日历，再依次根据这个流程进行。
<img src="https://i.loli.net/2019/09/18/yrbklW7Emf8V1Rj.jpg" width="80%" alt="项目流程" />

**linux测试环境搭建：**
1. 虚拟机自建环境（vmware等）
2. 购买云服务（>1C+2G)
3. 安装Docker

<!-- more -->

**Linux操作系统：**
1. CentOS
2. debian
3. Ubuntu

**Linux常见命令：**
1. 文档型：文件相关命令（touch、cat、echo、rm、vim、cd）
2. 硬件型：磁盘/进程/服务/网络
3. 功能型：压缩/解压缩、下载、远程


```
lsb_release -a //查看版本
top //查看CPU资源和内存资源
touch [name] //创建文件
cat [name] //查看文件内容
echo 'aaa' >> text.txt //尾行增加内容
echo 'aaa' > text.txt //覆盖全部内容
rm [filename] //删除文件
rm -r [dirname] //删除文件夹
rm -rf //强制删除，不要使用

wget [url] //下载资源
tar -zxvf file.tar.gz //加压，z-.gz结尾的文件 x-解压 v-显示所有解压过程 f-使用规定的名字name
tar zcvf directory filename.tar.gz //压缩
ps -ef | grep docker //筛选进程
kill -9 [pid] //强制杀死pid的进程
service [serverName] status //查看服务状态
service [serverName] stop //关闭服务
service [serverName] restart //重启服务
```

**ssh连接linux服务器**

可以直接采用 `ssh (-p 22) root@ip` 登录
1. 修改ssh的默认端口（建议，较为安全）

```
netstat -anlp | grep sshd //查看监听端口，默认为22
vi /etc/ssh/sshd_config //修改文件内容中的Port
接下来重启ssh服务即可
```
2. 实现免密钥登录服务器

```
ssh-keygen //生成ssh key
…… 自行搜索
```

**Docker**

Docker与虚拟机的区别：
<img src="https://i.loli.net/2019/09/22/O1rLmWl8JboGgs3.jpg" width="80%"/>

**为什么会出现docker这个技术？**

一款产品从开发到上线，从操作系统，到运行环境，再到应用配置，作为开发+运维之间的协作我们需要关心很多东西，这也是很多互联网公司不得不面对的问题，特别是各种版本迭代之后，不同版本环境的兼容，对运维人员都是考验。Docker之所以发展如此迅速，也是因为它对此给出了哟个标准化的解决方案。

不再是“搬家”（打包的代码），而是“搬楼”（打包代码、运行文档、配置环境、运行环境、运行依赖包、操作系统发行版等），把原始环境复制过来，消除了“在开发工程师机器上能跑，但在运维工程师机器上不能正常运行”的场景。

而且现在部署的都是集群环境，会存在多台服务器，每一台都重新安装，会耗费很多的时间和精力。

**理念：**

一次封装，到处运行。

**镜像/容器/仓库**

Docker镜像就是一个只读的模板，镜像可以用来创建Docker容器，一个镜像可以创建多个容器。简言之，镜像相当于类，容器相当于它的实例。

Docker利用容器独立运行一个或一组应用，容器是用镜像创建的运行实例。它可以被启动、开始、停止，每个容器都是相互隔离的、保证安全的平台。

我们可以把容器视为一个简易版的linux环境（包括root用户权限、进程空间、用户空间和网络空间）和运行在其中的应用程序。

仓库是集中存放镜像文件的场所。仓库和仓库注册服务器是有区别的，仓库注册服务器上往往存放着多个仓库，每个仓库中又包含了多个镜像，每个镜像有不同的标签tag。最大的仓库是Docker Hub（国外网站，非常慢），存放了数量庞大的镜像供用户下载，国内的公开仓库包括阿里云、网易云等。

**Docker容器技术的特点：**

1. 文件系统隔离：每个进程容器运行在完全独立的根文件系统里。
2. 资源隔离：可以使用cgroup为每个进程容器分配不同的系统资源，例如CPU和内存。
3. 网络隔离：每个进程容器运行在自己的网络命名空间里，拥有自己的虚拟接口和IP地址。
4. 写时复制：采用写时复制方式创建根文件系统，这让部署变得极其快捷，并且节省内存和硬盘空间。
5. 日志记录：Docker将会收集和记录每个进程容器的标准流（stdout/stderr/stdin），用于实时检索或批量检索。
6. 变更管理：容器文件系统的变更可以提交到新的映像中，并可重复使用以创建更多的容器。无需使用模板或手动配置。
7. 交互式Shell：Docker可以分配一个虚拟终端并关联到任何容器的标准输入上，例如运行一个一次性交互shell。

**Docker安装：**

[CentOS环境安装Docker步骤](https://www.runoob.com/docker/centos-docker-install.html)

```
// 移除旧的版本：
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine
// 安装一些必要的系统工具：
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
// 添加软件源信息：
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
// 更新 yum 缓存：
sudo yum makecache fast
// 安装 Docker-ce：
sudo yum -y install docker-ce
// 启动 Docker 后台服务
sudo systemctl start docker
// 测试运行 hello-world
docker run hello-world
```
到此，Docker 在 CentOS 系统的安装完成

[Centos7安装完毕后无法联网问题的解决方法](https://www.36nu.com/post/234)

**镜像加速**

修改/etc/docker/daemon.json（Linux），没有则新建

```
{
  "registry-mirrors": ["http://hub-mirror.c.163.com"]
}
```
vim中无法打开并写入文件的解决办法:`w ! sudo tee %`,tee 用于读取输入文件，同时保存，%表示当前编辑文件 

```
systemctl daemon-reload //重载配置文件
systemctl restart docker //重启docker
```
下次拉取docker镜像时，会直接拉取国内的源进行下载，从而加快速度。我们可以在[docker hub](https://hub.docker.com/)上下载镜像运行。

常见命令：
```
docker ps -a 列出正在运行的docker
docker images 查看正在运行的docker
docker run 运行容器
docker stop [name] 停止容器
docker start [name] 启动容器
docker restart [name] 重启容器
docker rm [name] 删除容器
docker logs -f [name] 查看容器详细运行信息
```
**docker-compose**

使用docker-compose可以同时管理多个容器，同时启动、停止，当容器数量很多时，能有效减少大量的人工操作。

安装docker-compose，注意提示权限不够时应提升权限，如使用sudo执行命令

```
curl -L https://github.com/docker/compose/releases/download/1.23.2/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
docker-compose --version //查看版本

sudo rm /usr/local/bin/docker-compose //卸载docker-compose
```
创建docker-compose.yml文件
```
version: '3'
services:
  web:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - .:/code
    environment:
      FLASK_ENV: development
  redis:
    image: "redis:alpine"
```
执行docker-compose up -d


**Docker Hub**
1. 注册docker hub账号
2. `docker login`登录
3. `docker commit -m "xx" [CONTAINER ID] username/repository:tag`提交，其中CONTAINER ID通过docker ps -a查看
4. `docker push username/repository:tag `上传
5. `docker pull username/repository:tag `拉取