#### MinIO简介

中文文档地址：https://docs.min.io/cn/

Minio中国镜像站：http://dl.minio.org.cn/

##### 在Docker中运行MinIO单点模式

安装：

Docker容器安装

下载镜像

```
docker pull minio/minio
```

运行容器

```
docker run -di --name myMinio -p 8660:9000 -e "MINIO_ACCESS_KEY=AKIAIOSFODNN7EXAMPLE" -e "MINIO_SECRET_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY" -v /data/minio/data:/data:z -v /data/minio/config:/root/.minio:z minio/minio server /data
```

```
docker run -di --name vianLocalMinio -p 9000:9000 -e "MINIO_ACCESS_KEY=VIANGZ" -e "VIANGZ" -v /data/minio/data:/data:z -v /data/minio/config:/root/.minio:z minio/minio server /data

```

1，进入容器 

```
docker exec -it vianLocalMinio sh
```

出现文件映射问题，没有权限：

解决：文件映射后加：Z

来源：https://github.com/minio/minio/issues/6237

https://www.projectatomic.io/blog/2016/03/dwalsh_selinux_containers/

防火墙开放端口后通过浏览器地址可以访问

http://39.105.45.130:8650/minio

##### 客户端操作MinIO服务器

中国镜像地址：http://dl.minio.org.cn/

下载文件地址：http://dl.minio.org.cn/client/mc/release/windows-amd64/mc.exe

用window10客户端操作服务器

控制台切换到程序目录

```
mc.exe --help
```

（这个是出现提示命令）

```
mc --autocompletion
```

（这个是启动程序配置项目，在默认的文件路径下会生成配置文件）

客户端添加服务器信息

```
mc config host add myMinio http://39.105.45.130:8690 AKIAIOSFODNN7EXAMPLE wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY --api s3v4
```

使用

```
mc ls myMinio
```

可以测试连接

实际上是在~/.mc/config.json中写入了如下配置

```
\# cat ~/.mc/config.json
{    
	"version": "9",    
	"hosts": {        
		"myminio": {            
		"url": "http://localhost:9001",
		"accessKey": "minio",
		"secretKey": "minio123",
		"api": "s3v4",
		"lookup": "auto"        
		}    
	}
}
```

```
"myMinio": {
			"url": "http://39.105.45.130:8650",
			"accessKey": "AKIAIOSFODNN7EXAMPLE",
			"secretKey": "wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY",
			"api": "s3v4",
			"lookup": "auto"
		}
```

新创建的bucket默认的访问policy是none，即外部无访问权限：

命令：

```
mc policy set download myMinio/weapp
```

返回：

```
Access permission for `myMinio/weapp` is set to `download`
```

命令：

```
mc policy get myMinio/weapp
```

返回：

```
Access permission for `myMinio/weapp` is `download`
```

接下来可以直接通过浏览器连接访问数据了

```
http://39.105.45.130:8650/weapp/teacher/avatar/老师头像01.png
```

