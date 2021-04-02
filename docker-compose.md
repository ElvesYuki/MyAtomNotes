# docker-compose

Compose是用于定义和运行多容器Docker应用程序的工具。通过Compose，您可以使用YAML文件来配置应用程序的服务。然后，使用一个命令，就可以从配置中创建并启动所有服务。要了解有关Compose的所有功能的更多信息，请参阅[功能列表](https://docs.docker.com/compose/#features)。



使Compose有效的功能包括：

- [单个主机上的多个隔离环境](https://docs.docker.com/compose/#multiple-isolated-environments-on-a-single-host)
- [创建容器时保留卷数据](https://docs.docker.com/compose/#preserve-volume-data-when-containers-are-created)
- [仅重新创建已更改的容器](https://docs.docker.com/compose/#only-recreate-containers-that-have-changed)
- [变量和在环境之间移动构图](https://docs.docker.com/compose/#variables-and-moving-a-composition-between-environments)



### 在Linux系统上安装

在Linux上，您可以从[GitHub上](https://github.com/docker/compose/releases)的[Compose存储库发行页面](https://github.com/docker/compose/releases)下载Docker Compose二进制文件 。按照链接中的说明进行操作，其中包括`curl`在终端中运行命令以下载二进制文件。这些分步说明也包含在下面。

```
对于`alpine`，需要以下依赖包： `py-pip`，`python3-dev`，`libffi-dev`，`openssl-dev`，`gcc`，`libc-dev`，`rust`，`cargo`和`make`。
```

#### 1、运行以下命令以下载Docker Compose的当前稳定版本：

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.28.6/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

> 要安装其他版本的Compose，请替换`1.28.6` 为要使用的Compose版本。

如果使用进行安装时遇到问题`curl`，请参见 上方的“[备用安装选项”](https://docs.docker.com/compose/install/#alternative-install-options)标签。



2、将可执行权限应用于二进制文件：

```
sudo chmod +x /usr/local/bin/docker-compose
```

> **注意**：如果`docker-compose`安装后命令失败，请检查路径。您还可以创建指向`/usr/bin`或路径中任何其他目录的符号链接。

例如：

```
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```



### 升级

如果要从Compose 1.2或更早版本进行升级，请在升级Compose之后删除或迁移现有容器。这是因为从1.3版开始，Compose使用Docker标签来跟踪容器，并且需要重新创建容器以添加标签。

如果Compose检测到创建的没有标签的容器，它将拒绝运行，这样您就不会最终获得两组标签。如果要继续使用现有容器（例如，因为它们具有要保留的数据量），则可以使用Compose 1.5.x通过以下命令迁移它们：

```
docker-compose migrate-to-labels
```

另外，如果您不担心保留它们，可以将其删除。撰写只是创建新的。

```
docker container rm -f -v myapp_web_1 myapp_db_1 ...
```

### 卸载

如果使用`curl`以下命令进行安装，则要卸载Docker Compose ：

```
sudo rm /usr/local/bin/docker-compose
```

如果使用`pip`以下命令进行安装，则要卸载Docker Compose ：

```
pip uninstall docker-compose
```





### 使用：

#### 1、编写docker-compose.yml配置文件

示例：

```
version: "3.9" //版本，随便定义，一般都是一样的
services:  // 服务
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



```
docker-compose -f docker-compose.yml up -d
```

