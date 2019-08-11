#### 1.什么是虚拟化

​	什么是虚拟化：资源管理技术，将计算机资源整合再分配。
​	虚拟化分类：
​		全虚拟化
​		操作系统级虚拟化
​		硬件级虚拟化

#### 2.什么是docker

​	什么是容器：
​		操作系统级虚拟化
​		容器技术：类似集装箱，封装软件及其依赖项
​	容器和虚拟机比较：
​		本质不同
​			虚拟机全虚拟化
​			容器是系统级虚拟化
​		大小不同
​			虚拟机占用空间大
​			容器占用空间小
​		速度不同
​			虚拟机运行速度慢
​			容器速度快
​		可移植性不同
​			虚拟机不如容器可以移植性好
​	docker特点：
​		上手快
​		职责分明：开发只关注软件，运维只关注容器
​		开发/移植/部署快
​		支持面向服务架构
​	docker组件：
​		客户端和服务端
​		镜像：类似于安装包，其中有运行环境（微型操作系统）/安装软件
​		注册中心（类似maven中央仓库，存储镜像）
​		容器：镜像的安装和运行环境
​			镜像
​			操作
​			执行环境

#### 3.docker安装

​	安装命令：
​		yum install docker
​	管理命令：
​		systemctl start docker
​		systemctl status docker
​		systemctl stop docker
​		systemctl restart docker
​		systemctl enable docker
​		docker -v
​		docker --help
​		docker info

#### 4.docker镜像操作：

​	docker images：列出镜像
​	docker pull 镜像名:版本号
​	修改镜像注册中心：
​		1)编辑/etc/docker/daemon.json
​		2)拷贝以下内容
​			{
​				"registry-mirrors": ["https://docker.mirrors.ustc.edu.cn"]
​			}
​		3)重启docker测试
​	搜索镜像：docker search 镜像名
​	删除镜像：docker rmi 镜像id

#### 5.docker容器操作：

​	创建容器
​		交互式：启动成功之后直接登录到镜像容器中，退出镜像容器后，容器自动关闭
​			docker run -it --name=mycentos centos:7 /bin/bash
​		守护式：启动之后，镜像容器自动后台执行，程序员可以继续在宿主机进行其他操作，运维时使用
​			docker run -di --name=mycentos2 centos:7
​	查看容器
​		docker ps -a查看所有
​		docker ps -l最后一次
​		docker ps 正在运行
​	停止或启动容器
​		docker stop 容器id 容器id2 ...
​		docker start 容器id 容器id2 ...
​		docker restart 容器id 容器id2 ...
​	删除容器
​		docker rm 容器id/名称
​		docker rm `docker ps -a -q`

#### 6.部署应用：

##### 	mysql部署

​		拉取镜像
​			docker pull mysql:5.5
​		创建容器
​			docker run -di --name=pinyougou_mysql -p 33306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql
​		进入容器
​			docker exec -it pinyougou_mysql /bin/bash
​		使用工具连接mysql
​			使用品优购建表语句创建表结构
​		查看容器信息：
​			docker inspect pinyougou_mysql

##### 	tomcat部署

​		拉取镜像
​			docker pull tomcat:7-jre7
​		创建容器
​			docker run -di --name pinyougou-tomcat -p 9000:8080 tomcat:7-jre7
​		部署cas项目：
​			修改cas项目数据源
​				改为pinyougou-mysql的ip
​			将cas文件夹上传到宿主机
​			将cas拷贝到docker的tomcat容器下
​				docker cp 宿主机cas目录 容器名:/usr/local/tomcat/webapps
​			重启容器
​			访问测试：
​				http://192.168.116.129:9000/cas/login

##### 	nginx部署

​		拉取镜像
​			docker pull nginx
​		创建容器
​			docker run -di --name pinyougou-nginx -p 80:80 nginx
​		测试容器
​			浏览器输入：http://192.168.116.129
​		配置反向代理
​			修改nginx.conf
​				upstream tomcat-cas {
​					server 172.17.0.5:8080;#pinyougou-tomcat的ip地址和端口号
​				}
​				server{
​					listen 80;
​					server_name passport.pinyougou.com;
​					location / {
​						proxy_pass http://tomcat-cas;
​						index index.html index.htm;
​					}
​				}
​			重启nginx服务器
​				docker restart pinyougou-nginx

##### 	redis部署

​		拉取镜像
​			docker pull redis
​		创建容器
​			docker run -di --name pinyougou-redis -p 6379:6379 redis
​		客户端测试
​			redis-cli -h 192.168.116.129

#### 7.备份与迁移

​	保存镜像
​		docker commit pinyougou-nginx mynginx
​	镜像打包
​		docker save -o mynginx.tar pinyougou_nginx
​	镜像恢复与迁移
​		docker load -i mynginx.tar

#### 8、docker挂在本地文件启动容器

pull 镜像

```
docker pull nginx
docker images
```

配置80端口映射 启动容器 

找到镜像中nginx.conf配置文件路径/etc/nginx/nginx.conf

用命令：cat  /etc/nginx/nginx.conf 查看nginx.conf文件内容然后拷贝出来

找到default.conf配置文件的路径/etc/nginx/conf.d/default.conf

用命令：cat  /etc/nginx/conf.d/default.conf 查看default.conf文件内容然后拷贝出来

找到默认首页文件夹html路径/usr/share/nginx/html

找到日志文件路径/var/log/nginx

然后输入exit退出容器的终端

在linux系统中创建挂载源文件和文件夹

```
mkdir -p /data/nginx/{conf,conf.d,html,logs}
```

将拷贝的文件赋值进去

```
docker run -di --name mynginx -p 80:80 -v /data/nginx/html:/usr/share/nginx/html -v /data/nginx/conf/nginx.conf:/etc/nginx/nginx.conf -v /data/nginx/conf.d:/etc/nginx/conf.d  -v /data/nginx/logs:/var/log/nginx nginx
```

有可能直接启动失败。在重新启动容器即可，可以在本地目录html下放网页了

#### 9、docker中安装 mysql5.5 并彻底解决中文乱码问题

1、获取mysql镜像：

```
docker pull mysql:5.5
```

2、创建mysql容器：

```
docker create --name mysql -v /data/mysql-data:/var/lib/mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql:5.5`  
```

解释：
--name mysql 指定是容器的名称
-v /data/mysql-data:/var/lib/mysql  将主机目录/data/mysql-data挂载到容器的/var/lib/mysql上
-p 3306:3306 设置端口映射，主机端口是3306，容器内部端口3306
-e MYSQL_ROOT_PASSWORD=123456  设置容器参数，设置root用户的密码为123456
mysql:5.5 镜像名:版本

3、直接创建运行容器

```
docker run -di --name=mysqlx -p 13380:3306 -e MYSQL_ROOT_PASSWORD=09120610 d404d78aa797
```

4、解决乱码问题

查看字符编码：

```
SHOW VARIABLES LIKE 'character_set_%';
```

进入mysql容器：

```
docker exec -it mysql /bin/bash
```

5、打开 my.cnf文件:

```
vim /etc/mysql/my.cnf
```

 在使用docker容器时键入vim命令时提示： vim: command not found
 这个时候就需要安装vim ,这时候需要敲:

```
apt-get update
```

 等更新完毕以后再敲命令:

```
apt-get install vim
```

然后你发现vim 编辑器可以使用以后，在此my.cnf文件中添加如下字段（注意配置的字段细节）：

1、在[client]字段里加入default-character-set=utf8，如下：

> ```
> [client]
>  port = 3306
>  socket = /var/lib/mysql/mysql.sock
>  default-character-set=utf8
> ```
>
> 

2、在[mysqld]字段里加入character-set-server=utf8，如下：

> ```
> [mysqld]
>  port = 3306
>  socket = /var/lib/mysql/mysql.sock
>  character-set-server=utf8
> ```
>
> 

3、在[mysql]字段里加入default-character-set=utf8，如下：

> ```
> [mysql]
>  no-auto-rehash
>  default-character-set=utf8
> ```

这次只有mysql这一个，结束后查看字符编码：

重启、查看。可能没有全部设置为utf8查看字符编码：

```
SHOW VARIABLES LIKE 'character_set_%';
```

手动设置

若外部访问数据乱码，可以进入数据库中，执行:

```
SET NAMES 'utf8';
```

`

它相当于下面的三句指令：

> ```
> SET character_set_client = utf8;
> SET character_set_results = utf8;
> SET character_set_connection = utf8;
> ```

查看字符编码：

```
SHOW VARIABLES LIKE 'character_set_%';
```

OK！