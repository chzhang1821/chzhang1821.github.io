---
layout:     post
title:      "Linux 软件安装"
subtitle:   
date:       2023-07-06 16:00
author:     "Chenghao"
header-img: "img/post-bg-2015.jpg"
tags:
    - Linux
---



# Linux 软件安装

## 1. 软件安装方式

* 二进制发布包安装

  软件已经针对具体平台编译打包发布，只要解压，修改配置即可

* rpm 安装

  软件已经按照 redhat 的包管理规范进行打包，使用 rpm 命令进行安装，不能自行解决库依赖问题

* yum 安装

  一种在线软件安装方式，本质上还是 rpm 安装，自动下载安装包并安装，安装过程中自动解决库依赖问题

* 源码编译安装

  软件以源码工程的形式发布，需要自己编译打包

## 2. 安装 JDK

操作步骤：

1. 将 jdk 的二进制发布包上传到 Linux `jdk-17_linux-x64_bin.tar.gz`

2. 解压安装包，命令为 `tar -zxvf jdk-17_linux-x64_bin.tar.gz -C /usr/local/java`

3. 配置环境变量，使用 vim 命令修改 `/etc/profile` 文件，在文件末尾加入如下配置

   ```bash
   JAVA_HOME=/usr/local/java/jdk-17.0.7
   PATH=$JAVA_HOME/bin:$PATH
   ```

4. 重新加载 profile 文件，使更改的配置立即生效，`source /etc/profile`
5. 检查安装是否成功，`java -version`

## 3. 安装 Tomcat

**操作步骤：**

1. 将 Tomcat 的二进制发布包上传到 Linux， `apache-tomcat-10.1.10.tar.gz`
2. 解压安装包，`tar -zxvf apache-tomcat-10.1.10.tar.gz`
3. 进入 Tomcat 的 bin 目录启动服务，`sh startup.sh` 或者 `./startup.sh`

**验证 Tomcat 启动是否成功，有多种方式：**

* 查看启动日志

  ```bash
  more /usr/local/java/apache-tomcat-10.1.10/logs/catalina.out
  tail -50 /usr/local/java/apache-tomcat-10.1.10/logs/catalina.out
  ```

* 查看进程 `ps -ef | grep tomcat`

  ```bash
  root      128689       1  1 23:14 pts/0    00:00:04 /usr/local/java/jdk-17.0.7/bin/java -Djava.util.logging.config.file=/usr/local/java/apache-tomcat-10.1.10/conf/logging.properties -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager -Djdk.tls.ephemeralDHKeySize=2048 -Djava.protocol.handler.pkgs=org.apache.catalina.webresources -Dorg.apache.catalina.security.SecurityListener.UMASK=0027 --add-opens=java.base/java.lang=ALL-UNNAMED --add-opens=java.base/java.io=ALL-UNNAMED --add-opens=java.base/java.util=ALL-UNNAMED --add-opens=java.base/java.util.concurrent=ALL-UNNAMED --add-opens=java.rmi/sun.rmi.transport=ALL-UNNAMED -classpath /usr/local/java/apache-tomcat-10.1.10/bin/bootstrap.jar:/usr/local/java/apache-tomcat-10.1.10/bin/tomcat-juli.jar -Dcatalina.base=/usr/local/java/apache-tomcat-10.1.10 -Dcatalina.home=/usr/local/java/apache-tomcat-10.1.10 -Djava.io.tmpdir=/usr/local/java/apache-tomcat-10.1.10/temp org.apache.catalina.startup.Bootstrap start
  root      129193  122822  0 23:17 pts/0    00:00:00 grep --color=auto tomcat
  ```

> 注意：
>
> * `ps` 命令是Linux下非常强大的进程查看命令，通过 `ps -ef` 可以查看当前运行的所有进程的详细信息
> * `|` 在Linux中称为管道符，可以将前一个命令的结果输出后给后一个命令作为输入
> * 使用 `ps` 命令查看进程时，经常配合管道符和查找命令 `grep` 一起使用，来查看特定进程

打开 `106.14.221.8:8080` ，注意防火墙操作，开放端口，云服务器注意在控制台的防火墙开启特定端口（详细操作：<https://chzhang1821.github.io/2023/06/26/centos7-firewall/>）

**停止 Tomcat 服务的方式：**

* 运行 Tomcat 的 bin 目录中提供的停止服务的脚本

  ```bash
  sh shutdown.sh
  ./shutdown.sh
  ```

* 结束 Tomcat 进程 - `kill -9 PID`

> 注意：
>
> `kill` 命令是 Linux 提供的用于结束进程的命令，`-9` 表示强制结束



## 4. 安装 MySQL

1. 检测当前系统中是否安装 MySQL 数据库

   ```bash
   rpm -qa										# 查询当前系统中安装的所有软件
   rpm -qa | grep mysql			# 查询当前系统中安装的名称带mysql的软件
   rpm -qa | grep mariadb		# 查询当前系统中安装的名称带mariadb的软件
   ```

   > 如果当前系统中已经安装有 MySQL 数据库，安装将失败。CentOS7 自带 mariadb，与 MySQL 数据库冲突

2. 卸载已经安装的冲突软件

   ``` bash
   rpm -e --nodeps 软件名称
   ```

3. 将 MySQL 安装包上传到 Linux 并解压

   ```bash
   mkdir /usr/local/mysql
   tar -zxvf mysql-8.0.33-linux-glibc2.28-x86_64.tar.gz
   ```

4. 将被解压的6个安装包按顺序安装

   ```bash
   rpm -ivh *-common*
   rpm -ivh *-libs*
   rpm -ivh *-devel*
   rpm -ivh *-libs-compat*
   rpm -ivh *-client*
   yum install net-tools
   rpm -ivh *-server*
   ```

5. 启动 MySQL

   ```bash
   systemctl status mysqld				# 查看mysql服务状态
   systemctl start mysqld				# 启动mysql服务
   # 可以设置开机时启动MySQL服务，避免每次开机启动MySQL
   systemctl enable mysqld				# 开机启动mysql服务
   
   netstat -tunlp								# 查看已经启动的服务
   netstat -tunlp | grep mysql
   
   ps -ef | grep mysql						# 查看mysql进程
   ```

6. 登录 MySQL 数据库，查阅临时密码

   ```bash
   cat /var/log/mysqld.log										# 查看文件内容
   cat /var/log/mysqld.log | grep password		# 查看文件内容包含password的行信息
   ```

7. 登录 MySQL，修改密码，开放访问权限

   ```bash
   mysql -u root -p																		# 登录mysql
   # 修改密码
   set global validate_password_length=4;							
   set global validate_password_policy=LOW;
   set password = password('root');
   # 开启访问权限
   grant all on *.* to 'root'@'%' identified by 'root';
   flush privileges;
   ```



## 5. 安装 lrzsz - Linux 中用于文件上传下载的软件

操作步骤：

1. 搜索 lrzsz 安装包，命令为 `yum list lrzsz`
2. 使用 yum 命令在线安装 - `yum install lrzsz.x86_64`

> Yum (全称为 Yellow dog Updater, Modified) 是一个在 Fedora 和 RedHat 以及 CentOS 中的 Shell 前端软件包管理器。基于 RPM 包管理，能够从指定的服务器自动下载 RPM 包并且安装，可以自动处理依赖关系，并且一次安装所有依赖的软件包。



## 6. 项目部署

### 6.1 手工部署项目

1. 在 IDEA 中开发 SpringBoot 项目并达成 jar 包 - `maven package` (打包前可以先用 `maven clean` 清理)，将 jar 包上传至 Linux，再用 `java -jar HelloWorld.jar` 

2. 检查防火墙，确保端口开放

3. 改为后台运行 SpringBoot 程序，并将日志输出到日志文件

   目前程序运行的问题：

   * 线上程序不会采用控制台霸屏的形式运行程序，而是将程序再后台运行
   * 线上程序不会将日志输出到控制台，而是输出到日志文件，方便运维查阅信息

   > `nohup` 命令：英文全称 no hang up (不挂起)，用于不挂断的运行指定命令，退出终端不会影响程序的运行
   >
   > 语法格式：`nohup Command [Arg ...] [&]`
   >
   > 参考说明：
   >
   > * `Command`：将要执行的命令
   > * `Arg`：一些参数，可以指定输出文件
   > * `&`：让命令在后台运行
   > * 举例：`nohup java -jar boot工程.jar &> hello.log &` 后台运行`java -jar`命令，并将日志输出到`hello.log`文件

4. 停止 SpringBoot 项目 - 杀进程



### 6.2 通过 Shell 脚本自动部署项目

操作步骤：

1. 在 Linux 中安装 Git

   1. ```bash
      cd /usr/loca/
      git clone https://github.com/chzhang1821/demo.git
      ```

2. 在 Linux 中安装 maven

   1. 将 maven 安装包上传到 Linux，在 Linux 中安装 maven

      ```bash
      tar -zxvf apache-maven-3.9.3-bin.tar.gz
      vim /etc/profile							# 修改配置文件，加入如下内容
      # maven_env
      export MAVEN_HOME=/usr/local/java/apache-maven-3.9.3
      export PATH=$JAVA_HOME/bin:$MAVEN_HOME/bin:$PATH
      
      source /etc/profile
      mvn -version									# 修改配置文件内容如下
      <localRepository>/usr/local/java/maven_repo</localRepository>
      ```

3. 编写 Shell 脚本（拉取代码、编译、打包、启动）

4. 为用户授予执行 Shell 脚本的权限

   > `chmod` （英文全称：change mode）命令是控制用户对文件的权限的命令
   >
   > Linux中的权限分为：读(r)、写(w)、执行(x)三种权限
   >
   > Linux的文件调用权限分为三级：文件所有者(Owner)、用户组(Group)、其他用户(Other users)
   >
   > 只有文件的所有者和超级用户可以修改文件或目录的权限
   >
   > 要执行Shell脚本需要有对此脚本文件的执行权限，如果没有则不能执行	

5. 执行 Shell 脚本

6. 设置静态ip

   <img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/linux_imgs/静态ip.png" alt = "静态ip" style = "zoom: 50%" />







<img src = "https://github.com/chzhang1821/chzhang1821.github.io/blob/master/img/linux_imgs/自动部署项目.png" alt = "自动部署项目" style = "zoom: 50%" />

