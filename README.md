# MyAtomNotes
我的本地笔记
test

##### 将本地仓库与远程仓库连接使用命令：

~~~
git pull origin master --allow-unrelated-histories
~~~

##### IDEA连接数据库手动设置时区：

```
jdbc:mysql://localhost:3306/Database?serverTimezone=UTC&characterEncoding=utf-8
```

##### 阿里云服务器MySQL设置utf8：

设置字符集：一般的，为了支持中文，我们应该将字符集设为 UTF-8， 执行SHOW VARIABLES LIKE 'character%';

查看当前 MySQL 字符集，默认服务器的字符器是 latin1 ，对中文不友好。修改 /etc/my.cnf 文件，添加字符集的设置

```
[mysql]

default-character-set = utf8

[mysqld]

character_set_server = utf8
```

重启 MySQL服务 ,可以看到字符集已经修改了

