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

**挂载启动**

```
docker run -di --privileged=true --name tomcat_system_service -v /data/tomcat/tomcat_system_service:/usr/local/tomcat/webapps -p 8602:8080 3037eb7cda68
```



#### 6.部署应用：

##### 	mysql部署

​		拉取镜像
​			docker pull mysql:5.5
​		创建容器
​			docker run -di --name=pinyougou_mysql -p 33306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql

```
docker run -di --name=myATGGMysql -p 3306:3306 -v /data/mysql/log:/var/log/mysql:z -v /data/mysql/data:/var/lib/mysql:z -v /data/mysql/conf:/etc/mysql:z -e MYSQL_ROOT_PASSWORD=root a4fdfd462add
```

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
​			docker run -di --name myredis -p 6323:6379 857c4ab5f029
​		客户端测试
​			redis-cli -h 192.168.116.129

```
docker run -p 6323:6379 --name myredis -v /data/redisdata/conf/redis.conf:/etc/redis/redis.conf -v /data/redisdata/data:/data -di 857c4ab5f029 redis-server /etc/redis/redis.conf --appendonly yes
```

```
docker run -p 6379:6379 --name myATGGRedis -v /data/redis/conf/redis.conf:/etc/redis/redis.conf:z -v /data/redis/data:/data:z -di 987b78fc9e38  redis-server /etc/redis/redis.conf
```



##### zookeeper部署：

docker run -di --name myzookeeperx -p 2181:2181 -p 2888:2888 -p 3888:3888  a873528df41f

##### ActiveMQ部署

docker run -di --name myactivemq -p 8699:8161 -p 8698:61616  3af156432993

##### Jenkins部署

```
docker run \
  -u root \
  -d \
  -p 8600:8080 \
  -p 50000:50000 \
  -v jenkins-data:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  jenkinsci/blueocean
```

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

vim conf 下的配置文件

```
[client]
default-character-set=utf8

[mysql]
default-character-set=utf8

[mysqld]
init_connect='SET collation_connection = utf8_unicode_ci'
init_connect='SET NAMES utf8'
character-set-server=utf8
collation-server=utf8_unicode_ci
skip-character-set-client-handshake
skip-name-resolve
```

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

#### 10、docker上tomcat配置虚拟路径

1.首先使用 docker pull tomcat 默认下载最新版的tomcat
2.运行tomcat docker run -it -p8801:8080 tomcat 然后CTRL+P+Q ：容器不停止退出
　　　-it:交互式打开一个终端
　　　-p:端口号映射
3.进入正在运行的tomcat容器 docker exec -it 运行中的tomcat容器 /bin/bash
4.此时进入虚拟机，由于没有vi和vim 命令 只能去下载
　　下载命令　
　　　１．apt-get update
　　　２．apt-get install vim
5.修改conf下的server.xml文件 vim conf/server.xml
6.在host标签中加入

```
 <Context path="" docBase="项目地址" reloadable="false" ></Context>
```

#### 11、SpringBoot项目Dockerfile部署到服务器

1、创建一个SpringBoot项目，并打成jar包；

2、上传到服务器目标地址文件夹下；

3、在同一目录下新建一个Dockerfile文件

```
# 基于java镜像创建新镜像
FROM java:8
VOLUME /tmp
# 将jar包添加到容器中
ADD weapp.jar /weapp.jar
# 运行jar包
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/weapp.jar"]
```

4、执行docker构建镜像命令

```
docker build -t weapp .
```

weapp表示镜像名称    . 表示当前目录

5、运行刚才构建成的镜像

```
docker run -di --name weapp -p 8690:8690 weapp
```

停止容器

```
docker stop weapp
```

删除容器

```
docker rm weapp
```

删除镜像

```
docker rmi weapp
```

#### 12、Docker安装minio

下载镜像

```
docker pull minio/minio
```

运行容器

```
docker run -di --name myMinio -p 8650:9000 -e "MINIO_ACCESS_KEY=AKIAIOSFODNN7EXAMPLE" -e "MINIO_SECRET_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY" -v /data/minio/data:/data -v /data/minio/config:/root/.minio minio/minio server /data
```

#### 13、Docker安装nacos

下载镜像

```
docker pull nacos/nacos-server
```

运行容器

```
docker run --env MODE=standalone --name myNacos -v /data/nacos/conf:/home/nacos/conf:z -di -p 8661:8848 nacos/nacos-server
```

nacos持久化配置：

mysql建表语句：

创建库：数据库全名 = nacos_config

```
/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = config_info   */
/******************************************/
CREATE TABLE `config_info` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `data_id` varchar(255) NOT NULL COMMENT 'data_id',
  `group_id` varchar(255) DEFAULT NULL,
  `content` longtext NOT NULL COMMENT 'content',
  `md5` varchar(32) DEFAULT NULL COMMENT 'md5',
  `gmt_create` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `gmt_modified` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '修改时间',
  `src_user` text COMMENT 'source user',
  `src_ip` varchar(20) DEFAULT NULL COMMENT 'source ip',
  `app_name` varchar(128) DEFAULT NULL,
  `tenant_id` varchar(128) DEFAULT '' COMMENT '租户字段',
  `c_desc` varchar(256) DEFAULT NULL,
  `c_use` varchar(64) DEFAULT NULL,
  `effect` varchar(64) DEFAULT NULL,
  `type` varchar(64) DEFAULT NULL,
  `c_schema` text,
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_configinfo_datagrouptenant` (`data_id`,`group_id`,`tenant_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='config_info';

/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = config_info_aggr   */
/******************************************/
CREATE TABLE `config_info_aggr` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `data_id` varchar(255) NOT NULL COMMENT 'data_id',
  `group_id` varchar(255) NOT NULL COMMENT 'group_id',
  `datum_id` varchar(255) NOT NULL COMMENT 'datum_id',
  `content` longtext NOT NULL COMMENT '内容',
  `gmt_modified` datetime NOT NULL COMMENT '修改时间',
  `app_name` varchar(128) DEFAULT NULL,
  `tenant_id` varchar(128) DEFAULT '' COMMENT '租户字段',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_configinfoaggr_datagrouptenantdatum` (`data_id`,`group_id`,`tenant_id`,`datum_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='增加租户字段';


/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = config_info_beta   */
/******************************************/
CREATE TABLE `config_info_beta` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `data_id` varchar(255) NOT NULL COMMENT 'data_id',
  `group_id` varchar(128) NOT NULL COMMENT 'group_id',
  `app_name` varchar(128) DEFAULT NULL COMMENT 'app_name',
  `content` longtext NOT NULL COMMENT 'content',
  `beta_ips` varchar(1024) DEFAULT NULL COMMENT 'betaIps',
  `md5` varchar(32) DEFAULT NULL COMMENT 'md5',
  `gmt_create` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `gmt_modified` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '修改时间',
  `src_user` text COMMENT 'source user',
  `src_ip` varchar(20) DEFAULT NULL COMMENT 'source ip',
  `tenant_id` varchar(128) DEFAULT '' COMMENT '租户字段',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_configinfobeta_datagrouptenant` (`data_id`,`group_id`,`tenant_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='config_info_beta';

/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = config_info_tag   */
/******************************************/
CREATE TABLE `config_info_tag` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `data_id` varchar(255) NOT NULL COMMENT 'data_id',
  `group_id` varchar(128) NOT NULL COMMENT 'group_id',
  `tenant_id` varchar(128) DEFAULT '' COMMENT 'tenant_id',
  `tag_id` varchar(128) NOT NULL COMMENT 'tag_id',
  `app_name` varchar(128) DEFAULT NULL COMMENT 'app_name',
  `content` longtext NOT NULL COMMENT 'content',
  `md5` varchar(32) DEFAULT NULL COMMENT 'md5',
  `gmt_create` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `gmt_modified` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '修改时间',
  `src_user` text COMMENT 'source user',
  `src_ip` varchar(20) DEFAULT NULL COMMENT 'source ip',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_configinfotag_datagrouptenanttag` (`data_id`,`group_id`,`tenant_id`,`tag_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='config_info_tag';

/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = config_tags_relation   */
/******************************************/
CREATE TABLE `config_tags_relation` (
  `id` bigint(20) NOT NULL COMMENT 'id',
  `tag_name` varchar(128) NOT NULL COMMENT 'tag_name',
  `tag_type` varchar(64) DEFAULT NULL COMMENT 'tag_type',
  `data_id` varchar(255) NOT NULL COMMENT 'data_id',
  `group_id` varchar(128) NOT NULL COMMENT 'group_id',
  `tenant_id` varchar(128) DEFAULT '' COMMENT 'tenant_id',
  `nid` bigint(20) NOT NULL AUTO_INCREMENT,
  PRIMARY KEY (`nid`),
  UNIQUE KEY `uk_configtagrelation_configidtag` (`id`,`tag_name`,`tag_type`),
  KEY `idx_tenant_id` (`tenant_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='config_tag_relation';

/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = group_capacity   */
/******************************************/
CREATE TABLE `group_capacity` (
  `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `group_id` varchar(128) NOT NULL DEFAULT '' COMMENT 'Group ID，空字符表示整个集群',
  `quota` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '配额，0表示使用默认值',
  `usage` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '使用量',
  `max_size` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单个配置大小上限，单位为字节，0表示使用默认值',
  `max_aggr_count` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '聚合子配置最大个数，，0表示使用默认值',
  `max_aggr_size` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单个聚合数据的子配置大小上限，单位为字节，0表示使用默认值',
  `max_history_count` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '最大变更历史数量',
  `gmt_create` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `gmt_modified` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '修改时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_group_id` (`group_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='集群、各Group容量信息表';

/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = his_config_info   */
/******************************************/
CREATE TABLE `his_config_info` (
  `id` bigint(64) unsigned NOT NULL,
  `nid` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
  `data_id` varchar(255) NOT NULL,
  `group_id` varchar(128) NOT NULL,
  `app_name` varchar(128) DEFAULT NULL COMMENT 'app_name',
  `content` longtext NOT NULL,
  `md5` varchar(32) DEFAULT NULL,
  `gmt_create` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `gmt_modified` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `src_user` text,
  `src_ip` varchar(20) DEFAULT NULL,
  `op_type` char(10) DEFAULT NULL,
  `tenant_id` varchar(128) DEFAULT '' COMMENT '租户字段',
  PRIMARY KEY (`nid`),
  KEY `idx_gmt_create` (`gmt_create`),
  KEY `idx_gmt_modified` (`gmt_modified`),
  KEY `idx_did` (`data_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='多租户改造';


/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = tenant_capacity   */
/******************************************/
CREATE TABLE `tenant_capacity` (
  `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `tenant_id` varchar(128) NOT NULL DEFAULT '' COMMENT 'Tenant ID',
  `quota` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '配额，0表示使用默认值',
  `usage` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '使用量',
  `max_size` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单个配置大小上限，单位为字节，0表示使用默认值',
  `max_aggr_count` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '聚合子配置最大个数',
  `max_aggr_size` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单个聚合数据的子配置大小上限，单位为字节，0表示使用默认值',
  `max_history_count` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '最大变更历史数量',
  `gmt_create` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `gmt_modified` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '修改时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_tenant_id` (`tenant_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='租户容量信息表';


CREATE TABLE `tenant_info` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `kp` varchar(128) NOT NULL COMMENT 'kp',
  `tenant_id` varchar(128) default '' COMMENT 'tenant_id',
  `tenant_name` varchar(128) default '' COMMENT 'tenant_name',
  `tenant_desc` varchar(256) DEFAULT NULL COMMENT 'tenant_desc',
  `create_source` varchar(32) DEFAULT NULL COMMENT 'create_source',
  `gmt_create` bigint(20) NOT NULL COMMENT '创建时间',
  `gmt_modified` bigint(20) NOT NULL COMMENT '修改时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_tenant_info_kptenantid` (`kp`,`tenant_id`),
  KEY `idx_tenant_id` (`tenant_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='tenant_info';

CREATE TABLE `users` (
	`username` varchar(50) NOT NULL PRIMARY KEY,
	`password` varchar(500) NOT NULL,
	`enabled` boolean NOT NULL
);

CREATE TABLE `roles` (
	`username` varchar(50) NOT NULL,
	`role` varchar(50) NOT NULL,
	UNIQUE INDEX `idx_user_role` (`username` ASC, `role` ASC) USING BTREE
);

CREATE TABLE `permissions` (
    `role` varchar(50) NOT NULL,
    `resource` varchar(512) NOT NULL,
    `action` varchar(8) NOT NULL,
    UNIQUE INDEX `uk_role_permission` (`role`,`resource`,`action`) USING BTREE
);

INSERT INTO users (username, password, enabled) VALUES ('nacos', '$2a$10$EuWPZHzz32dJN7jexM34MOeYirDdFAZm2kuWj7VEOJhhZkDrxfvUu', TRUE);

INSERT INTO roles (username, role) VALUES ('nacos', 'ROLE_ADMIN');

```

进入容器：

```
docker exec -it myNacos /bin/bash
```

找到配置文件 /home/conf/application.properties

添加：

```
spring.datasource.platform=mysql

db.num=1
db.url.0=jdbc:mysql://172.17.0.2:3306/nacos_config?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true
db.user=root
db.password=root
```

