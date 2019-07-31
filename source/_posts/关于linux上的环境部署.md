---
title: linux node项目环境部署
date: 2019-01-03
categories: linux
author: yangpei
tags:
  - linux
comments: true
cover_picture: /images/banner.jpg
---

**阿里云服务器：**
1、连接到服务器
2、安装最新版本 node+npm（查看版本），已自带 node 和 npm 环境（实现选择的镜像）

<!-- more -->

3、Centos 安装 mysql：https://www.cnblogs.com/huangxinyuan650/p/6360464.html
4、mysql 运行 sql 导入数据库（pwd：123456）
https://blog.csdn.net/askycat/article/details/70991389
5、安装 curl 工具（配置环境变量？测试一下 curl 命令）
http://www.souvc.com/?p=1779
https://blog.csdn.net/qq_34827048/article/details/73564292
6、上传项目文件到服务器（使用 WinSCP，进行远程传输）https://jingyan.baidu.com/article/ceb9fb10a8dbb78cad2ba03a.html
7、安装 geoserver，不需要安装 tomcat
https://www.jianshu.com/p/0f25646963f3
http://39.108.227.136:8080/geoserver/web/——打开geoserver页面
startup.sh: command not found
命令前加上 bash 或 sh 或 ./
.代表当前目录，如果执行程序或脚本没有加入环境变量 PATH，在当前目录时前面要加"./"
关于 LINUX 权限-bash: ./startup.sh: Permission denied
用命令 chmod 修改一下 bin 目录下的.sh 权限就可以了
如 chmod u+x \*.sh
8、批量发布 xml 和 shp，publicShp.js 中的 xml 要为绝对路径，才能正常发布
9、npm start 启动程序，要另外添加 3800 端口（允许访问）
10、Centos 安装图形界面（不建议）
安装引导过程中可以选择图形界面（Gnome、KDE……）
https://www.linuxidc.com/Linux/2017-03/141348.htm
https://blog.csdn.net/m0_37903789/article/details/84504589
11、安装代码编辑器（可选）
https://blog.csdn.net/zdhsoft/article/details/73457259——vscode
https://github.com/spf13/spf13-vim——配置vim
12、X2Go Client 下载与使用（另一终端工具，可选）
https://blog.csdn.net/qq_17105473/article/details/74597343——windows安装
https://blog.csdn.net/iloveyin/article/details/48490723——linux配置

---

**问题解决：**
升级 node 版本：https://segmentfault.com/a/1190000015302680
在后台运行 geoserver：https://blog.csdn.net/ruiyelp/article/details/80184249
   1. command & ： 后台运行，你关掉终端会停止运行
   2. nohup command & ： 后台运行，你关掉终端也会继续运行
复制粘贴：ctrl+shift+v
添加环境变量（GEOSERVER_DATA_DIR）：https://www.cnblogs.com/whoamme/p/4039998.html
Vim 优化：https://github.com/spf13/spf13-vim

---

**目录问题：**
下载的软件存放位置  
/var/cache/apt/archives
安装后软件默认位置  
/usr/share
可执行文件位置  
/usr/bin
配置文件位置  
/etc
lib 文件位置  
/usr/lib
桌面文件
/home/yourname/desktop

/usr：系统级的目录，可以理解为 C:/Windows/，/usr/lib 理解为 C:/Windows/System32。
/usr/local：用户级的程序目录，可以理解为 C:/Progrem Files/。用户自己编译的软件默认会安装到这个目录下。
/opt：用户级的程序目录，可以理解为 D:/Software，opt 有可选的意思，这里可以用于放置第三方大型软件（或游戏），当你不需要时，直接 rm -rf 掉即可。在硬盘容量不够时，也可将/opt 单独挂载到其他磁盘上使用。
源码放哪里？
/usr/src：系统级的源码目录。
/usr/local/src：用户级的源码目录。
