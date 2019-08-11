## 阿里云服务器

​		控制台记得开启外部访问端口

​		访问工具：

​				xshell

​				xftp

​		

# Linux搭建web环境手册

## 说明

```
说明：该笔记是在linux上安装jdk,mysql,tomcat,redis软件，以便于建立一个linux系统下的web环境
建议：保留该安装笔记 以便复用
```

## 软件安装命令：rpm

```
作用：相当于软件助手，可以查询已安装的软件、卸载软件和安装软件。
安装：rpm -ivh 软件名  例如：rpm -ivh jdk
卸载：rpm -e --nodeps 软件名 rpm -e --nodeps mysql
查看所有安装的软件：rpm -qa 	例如查所有的安装软件中是否有tomcat软件：rpm -qa | grep tomcat
```

## 安装准备

```
在/usr/local目录下创建jdk文件夹，mysql文件夹，tomcat文件夹，redis文件夹存放安装内容
```

## jdk安装

```
步骤1：查看当前Linux系统是否已经安装jdk
命令 rpm -qa | grep java    注意:在7中没有 在6中有
步骤2：将jdk压缩包放入/usr/local/jdk目录下
命令 mv jdk-8u181-linux-x64.tar.gz /usr/local/jdk
步骤3：进入 /usr/local/jdk目录下,解压jdk
命令 cd /usr/local/jdk
命令 tar -zxvf jdk-8u181-linux-x64.tar.gz
测试 java -version(不成功,得配置环境变量)
步骤4：配置环境变量
命令 vi /etc/profile
在文件的最后面添加如下代码  
export JAVA_HOME=/usr/local/jdk/jdk1.8.0_181
export PATH=$JAVA_HOME/bin:$PATH
ps:这个路径配置的是自己jdk解压之后的路径
步骤5：重新加载配置文件
命令 source /etc/profile
```

最终测试: java -version

```
java version "1.8.0_181"
Java(TM) SE Runtime Environment (build 1.8.0_181-b13)
Java HotSpot(TM) 64-Bit Server VM (build 25.181-b13, mixed mode)
出现这些东西,证明安装全部成功
```

## tomcat安装

```
步骤1：查看当前Linux系统是否已经安装tomcat
命令rpm -qa | grep tomcat
步骤2：将tomcat压缩包放入/usr/local/tomcat目录下
命令 mv apache-tomcat-8.5.27.tar.gz /usr/local/tomcat
步骤3：进入/usr/local/tomcat目录下,解压tomcat
命令 cd /usr/local/tomcat
命令 tar -zxvf apache-tomcat-8.5.27.tar.gz
步骤4: 进入/usr/local/tomcat/apache-tomcat-8.5.27/bin 启动tomcat
命令 ./startup.sh
测试: 通过浏览器访问8080端口(不成功,得配置8080端口开放)
步骤5: 修改防火墙的配置 文件
命令 vi /etc/sysconfig/iptables
复制(yy)	
-A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
粘贴(p)
-A INPUT -m state --state NEW -m tcp -p tcp --dport 8080 -j ACCEPT
步骤6:重启防火墙服务					
service iptables restart
```

## tomcat虚拟路径的2种方式发布项目

1 配置server.xml，添加context标签

在server.xml配置文件的最后加上如下代码：

```
<Context path="项目的浏览器访问地址名" docBase="项目所在的硬盘位置" />
```

谨慎使用（最好单独独立出来一个配置文件）



2 配置独立xml文件

在tomcat/conf目录下新建一个Catalina目录（如果已经存在无需创建）

在Catalina目录下创建localhost目录（如果已经存在无需创建）

在localhost中创建xml配置文件，名称为：xxx.xml（xxx名就是项目的浏览器访问地址名）

xxx.xml中代码如下：

```
<Context docBase="项目所在的硬盘位置" />
```

好处：使用配置文件对项目的部署和卸载不用重启tomcat了，也不影响tomcat整体的配置文件



## mysql安装

```
步骤1：查看当前Linux系统是否已经安装mysql
命令 rpm -qa | grep mysql
虽然没有安装mysql,但是有自带的数据库：mariadb
步骤2：查看并卸载自带的数据库
查看命令 rpm -qa | grep mariadb
卸载命令 rpm -e --nodeps mariadb-libs-5.5.56-2.el7.x86_64
步骤3：将mysql压缩包放入/usr/local/mysql目录下,解压mysql
命令 mv MySQL-5.6.22-1.el6.i686.rpm-bundle.tar /usr/local/mysql
命令 tar -xvf MySQL-5.5.49-1.linux2.6.i386.rpm-bundle.tar
步骤4：先安装解压后的服务器端(MySQL-server-5.6.22-1.el6.i686.rpm)
命令 rpm -ivh MySQL-server-5.5.49-1.linux2.6.i386.rpm
ps：会缺依赖,安装mysql服务器端软件需要依赖如下(4个依赖包)
libaio.so.1
libc.so.6
libgcc_s.so.1(这个版本有冲突，需要先卸载再安装)
libstdc++.so.6（这个版本有冲突，需要先卸载在安装）
步骤5：安装依赖
命令 yum install libaio.so.1
命令 yum install libc.so.6
步骤6：先卸载 libgcc 再安装 libgcc
卸载命令
rpm -qa|grep libgcc
rpm -e --nodeps libgcc-4.8.5-28.el7_5.1.x86_64
安装命令
yum install libgcc_s.so.1
步骤7：先卸载libstdc++再安装libstdc++
卸载命令
rpm -qa|grep libstdc
rpm -e --nodeps libstdc++-devel-4.8.5-28.el7_5.1.x86_64 
rpm -e --nodeps libstdc++-4.8.5-28.el7_5.1.x86_64
安装命令
yum install libstdc++.so.6
步骤8：重新执行安装服务器端命令
命令 rpm -ivh  MySQL-server-5.5.49-1.linux2.6.i386.rpm
ps:在安装的过程中,记得复制这段设置密码的格式文本---用来设置登录密码的
设置登录密码的格式：/usr/bin/mysqladmin -u root password '要设置的登录密码'
步骤9：安装客户端(MySQL-client-5.5.49-1.linux2.6.i386.rpm)
命令 rpm -ivh MySQL-client-5.5.49-1.linux2.6.i386.rpm
ps：会缺依赖,安装mysql客户端端也需要依赖如下软件
libncurses.so.5
执行安装依赖命令:yum install libncurses.so.5
再次执行：rpm -ivh MySQL-client-5.5.49-1.linux2.6.i386.rpm
步骤10：启动mysql服务
命令 service mysql start
步骤11：将mysql设置开机自动启动服务
命令 systemctl enable mysql
步骤12: 设置密码操作
/usr/bin/mysqladmin -u root password '密码'
步骤13：登录mysql
命令 mysql -u用户名 -p密码


使用远程sqlyog图形化界面方式
步骤14：修改防火墙的配置文件(放行3306端口号)
命令 vi /etc/sysconfig/iptables
复制(yy)	
-A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
粘贴(p)
-A INPUT -m state --state NEW -m tcp -p tcp --dport 3306 -j ACCEPT
重启防火墙服务:service iptables restart	
步骤15: 允许远程连接linux下的mysql
默认情况下mysql为安全起见,不支持远程登录mysql,所以需要设置开启远程登录mysql的权限登录mysql
权限设置命令：grant all privileges on *.* to '用户名'@'%' identified by '密码';
刷新权限命令：flush privileges;
```

## linux下安装的mysql中文乱码解决问题

```
查看服务器的编码：show variables like '%char%'; 字符集编码为latin1 不是utf-8
解决：
步骤1：停止mysql服务器
步骤2：将/usr/share/mysql/my-medium.cnf 复制到/etc目录下,且重命名为my.cnf
步骤3：编辑my.cnf,在[mysqld]下添加一行"character-set-server=utf8" 保存退出
步骤4：重启mysql服务器 新建数据库查看编码
```

## redis安装

```
步骤1: yum install gcc-c++  //因为是C语言编写的,需要C语言的环境(下载过就不需要了)
步骤2: 将redis压缩包放入/usr/local/redis目录下
命令 mv redis-3.0.7.tar.gz /usr/local/redis
步骤3：进入/usr/local/redis目录下,解压redis
命令 cd /usr/local/redis		
     tar -zxvf redis-3.0.7.tar.gz	
步骤4: 进入解压文件编译并安装redis
命令 cd redis-3.0.7
     make
     make PREFIX=/usr/local/redis install
安装成功之后 在redis目录下多出来一个bin目录
步骤5：启动(服务器)
将redis-3.0.7目录下的redis.conf文件复制到 /usr/local/redis/bin 下
命令 cp redis.conf ../usr/local/redis/bin/		 
修改redis.conf配置文件 设置为启动服务加载配置文件
命令 vi redis.conf
搜索/daemonize
将 daemonize 值改成yes即可
启动: ./redis-server redis.conf     
步骤6：使用redis的客户端连接redis服务器			
连接指定主机 指定端口号			
命令 ./redis-cli -h ip -p 端口 	

步骤7：如果要使用可视化图形工具连接 要修改防火墙的配置文件
命令 vi /etc/sysconfig/iptables
复制(yy)	
-A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
粘贴(p)
-A INPUT -m state --state NEW -m tcp -p tcp --dport 6379 -j ACCEPT
步骤7:重启防火墙服务					
service iptables restart
步骤8:关闭服务器
./redis-cli -h ip -p 端口 shutdown
或kill -9 pid
```

## Nginx的安装

```
1安装C语言环境（gcc）
命令：yum install gcc-c++   安装期间有提示，一律选y

2安装Nginx依赖环境，‐y表示所有提示默认选择y
yum -y install pcre pcre-devel
yum -y install zlib zlib-devel
yum -y install openssl openssl-devel

3放入/usr/local下解压并进入解压目录
tar -zxvf nginx-1.13.9.tar.gz
cd /usr/local/nginx-1.13.9

4编译并安装
./configure
make
make install
ps:安装成功之后,就会在/usr/local下多出了一个nginx目录.

5启动/停止 Nginx
进入nginx的sbin目录:cd /usr/local/nginx/sbin
./nginx  启动
./nginx -s stop 停止 

6 在防火墙配置文件中开放80端口
vi /etc/sysconfig/iptables
重启防火墙服务：service iptables restart

7 查看是否有nginx的线程是否存在
命令 ps -ef | grep nginx
```

## 配置Nginx的反向代理

```
步骤一：修改nginx的配置文件
命令 vim nginx.conf
步骤二：增加或修改如下内容
server(服务器)上增加代理

upstream tomcat{server localhost:8080;} #为谁代理服务
server {
　　　　listen 80;
　　　　server_name localhost;
　　　　location / {
           # root   html;
           # index  index.html index.htm;
           
           proxy_pass http://tomcat; # 访问tomcat
        }
}
步骤三：重启nginx
命令 ./nginx -s reload
```

## Firewall开启常见端口命令：

```
注意:

--permanent意思是:永久生效 firewall-cmd --zone=public --add-port=80/tcp --permanent firewall-cmd --
zone=public --add-port=443/tcp --permanent firewall-cmd --zone=public --add-port=22/tcp --permanent
firewall-cmd --zone=public --add-port=21/tcp --permanent firewall-cmd --zone=public --add-port=53/udp --
permanent
**Firewall关闭常见端口命令**：
firewall-cmd --zone=public --remove-port=80/tcp --permanent firewall-cmd --zone=public --remove-
port=443/tcp --permanent firewall-cmd --zone=public --remove-port=22/tcp --permanent firewall-cmd --
zone=public --remove-port=21/tcp --permanent firewall-cmd --zone=public --remove-port=53/udp --
permanent
**批量添加区间端口**
firewall-cmd --zone=public --add-port=4400-4600/udp --permanent 
firewall-cmd --zone=public --add-port=4400-4600/tcp --permanent
firewall-cmd --zone=public --add-port=8600-8700/tcp --permanent
**查看某个端口是否开放**
firewall-cmd --zone=public --query-port=22/tcp firewall-cmd --query-port=8080/tcp
**查看端口列表**：
firewall-cmd --permanent --list-port
**开启防火墙命令**：
systemctl start firewalld.service
**重启防火墙命令**：
firewall-cmd --reload 或者 service firewalld restart
**禁用防火墙**
systemctl stop firewalld
**设置开机启动**
systemctl enable firewalld
**停止并禁用开机启动**
sytemctl disable firewalld 或者 sytemctl mask firewalld

**查看状态**
systemctl status firewalld或者 firewall-cmd --state
```



## 用户管理

在Linux系统中，除超级管理员外,还可以创建用户,创建组

```
1 创建新用户
命令: useradd  -m  新用户名 
ps:在home目录下会多一个用户 默认有一个组:组名就是当前用户名

2 设置新用户密码
命令: passwd  用户名
ps:普通用户的密码设置需要8位数的

3 使用新用户登录linux
使用新创建的用户名和密码登录 
ps:#代表超级管理员 $代表普通用户

4 修改用户名
命令 usermod -l 新用户名 旧用户名
ps:修改旧用户名为新用户名，该用户所在组名不变 还是以前的名字
   旧用户名就不能再登录Linux了 必须要使用新用户名

5 创建新用户组
命令：groupadd   新组名

6 修改用户组名
命令：groupmod  -n 新组名 旧组名

7 将用户添加到新用户组中
命令：usermod  -g 组名 用户名

8 从新用户组中删除用户
命令：gpasswd  -d 用户名  用户组名
```

## SSH免密登录

在企业中，多台Linux机器是可以相互之间进行登录和退出的。
用Linux自带的SSH客户端命令，可以用输入密码的方式登录到另一台Linux机器中

```
命令:ssh 另一台linux主机的地址  需要密码
退出：exit
```

发现每次登录另一台Linux机器都要输入用户名和密码，难免让用户觉得麻烦。那么能不能
一个用户在不输入密码的情况下，也能登录另一台Linux机器呢，答案是可以的
这就是SSH免密登录(多台linux操作系统主机互相登录)

```
基于一对密钥：也就是你必须为自己创建一对密钥(公钥和私钥)，并把公匙放在要访问的服务器上
密钥生成命令：ssh-keygen  三次回车
将公钥给另一台服务器：ssh-copy-id 另一台linux服务器的ip
再次登录:ssh 另一台linux服务器的ip  就再也不用密码了
```

## 修改主机名命令

需求：想给linux主机取个有含义的主机名，方便将来查询

```
查看主机名：hostname
临时修改：
修改主机名：hostname 新主机名
此时修改的主机名只在当前服务中有效果，服务重启将还原主机名
永久设置：
hostnamectl set-hostname  主机名
```

## ip地址设置--自动配置/手动配置命令

需求：之前我们linux的ip地址都是安装的时候自动分配获取的，能不能设置一个固定的ip地址

```
查看网络配置：ifconfig
设置固定ip：
编辑ifcfg-ens33网卡文件 vi /etc/sysconfig/network‐scripts/ifcfg‐ens33
```

信息如下：

```
DEVICE=ens33 #网卡名称
TYPE=Ethernet #网卡类型 以太网
ONBOOT=yes #是否开机就使用此网卡 
BOOTPROTO=dhcp #启动网卡时指定获取IP地址的方式
常用取值 : dhcp （自动获取ip地址,网关,子网掩码等信息无需设置）
常用取值：static （静态ip,如需要访问网络,需要自己设置ip地址等信息）
IPADDR=192.168.50.128 #ip地址
GATEWAY=192.168.50.2 #网关
NETMASK=255.255.255.0 #子网掩码
```

注意：修改完毕后要重启网卡服务：service network restart

```
总结：linux的ip地址2种配置方式
linux的主机自动获取ip：BOOTPROTO=dhcp
会自动给当前linux主机分配ip地址

linux的主机手动设置ip：BOOTPROTO=static
手动自己配置ip 网关 子网掩码
IPADDR=192.168.50.128 #ip地址
GATEWAY=192.168.50.2 #网关
NETMASK=255.255.255.0 #子网掩码
还需要重启网卡服务 加载修改过后的ifcfg‐ens33配置文件
```

## ip和域名的映射

我们访问自己电脑的时候可以使用127.0.0.1访问,也可以通过localhost访问,其实是由于localhost这个域名映射到了127.0.0.1这个ip地址上了.

同理,我们也可以设置域名映射,把要访问的虚拟机ip地址在当前主机上映射一下

设置：将linux的ip地址进行域名映射

```
更改/etc下的hosts文件 最下面添加ip和域名的映射
192.168.xx.xx  ee91
```

## 网络服务管理(对应的是window的服务管理)

例如：service network(网卡服务) restart    重启网卡服务

```
服务命令：
service 服务名称 status  -----查看服务状态
service 服务名称 start   -----开启服务
service 服务名称 stop    -----关闭服务
service 服务名称 restart -----重启服务
或
systemctl status  服务名称 -----查看服务状态
systemctl start   服务名称 -----开启服务
systemctl stop    服务名称 -----关闭服务
systemctl restart 服务名称 -----重启服务
```

配置服务开机自启动

```
mysql window  开机自动启动的
mysql linux   开机不自动启动的

开机自启/禁用服务
chkconfig mysql off 关掉服务的开机自动启动
chkconfig mysql on 开启服务的开机自动启动
或
systemctl enable mysql  NetworkManager  开启network服务的开机启动
systemctl disable mysql NetworkManager 关闭network服务的开机启动
```

## 进程端口的查看

```
端口号的查看：netstat -apn
端口号冲突 可以进行查看谁占用了 方便结束掉占用端口的进程    kill -9 pid
```

## 防火墙设置

```
1 安装iptables作为防火墙(企业用)
yum install iptables-services    #通过yum install 命令可以从网上下载安装iptables

2 停止firewall及其开机启动
systemctl stop firewalld.service   #停止firewall
systemctl disable firewalld.service   #禁止firewall开机启动

3 启用iptables,配置开机启动
systemctl start iptables.service   #启动iptables 
systemctl enable iptables.service  #将iptables设置为开机启动

4 查看iptables是否安装成功：systemctl status iptables
关闭iptables防火墙：systemctl stop iptables  #严重不建议
重启iptables防火墙：systemctl restart iptables

ps:只要安装好了iptable这个防火墙，这个防火墙的配置文件在/etc/sysconfig/iptabls
iptabls配置文件：允许某个端口访问
后期我们要安装软件 比如tomcat 8080
我们可以通过修改iptables文件设置8080端口可以被外界访问
修改配置文件：vi /etc/sysconfig/iptables  复制指定端口
ps:iptables是iptable防火墙的配置文件 所以必须得安装iptable防火墙才有该配置文件
```