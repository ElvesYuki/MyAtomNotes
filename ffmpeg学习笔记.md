## ffmpeg学习笔记

### 1、简介



### 2、安装

0.前提准备：使用yum安装编译工具 gcc cc cl

```
yum -y install gcc cc cl
```

1.安装ffmpeg时需要提前安装yasm插件

- 1)下载

  ```
  wget http://www.tortall.net/projects/yasm/releases/yasm-1.3.0.tar.gz
  ```

- 2)解压

  ```
  tar -xvf yasm-1.3.0.tar.gz
  ```

- 3)进入解压后的文件夹路径下，执行安装

  ```
  cd yasm-1.3.0/
  ./configure && make && make install
  ```

可能没有x264编码器

```
Unknown encoder 'h264'
```

```
解决方案：
1.安装x264

git clone https://code.videolan.org/videolan/x264.git
cd x264
#查看帮助信息
./configure --help
#我们需要的是x264以.so or .a的形式来支援ffmpeg，所以一般就关注shared和static关键词就可以了
./configure --enable-shared --enable-static
make
make install
```

```
yum install pkg-config
```

2.安装FFmpeg

- 1)下载

  ```
  wget http://www.ffmpeg.org/releases/ffmpeg-4.3.tar.gz
  ```

- 2)解压

  ```
  tar -xvf ffmpeg-4.3.tar.gz
  ```

- 3)进入解压后的文件夹路径下，执行安装

  ```
  cd ffmpeg-4.3/
  ./configure && make && make install
  
  //使用下面这个编译
  ./configure --enable-shared --enable-yasm --enable-libx264 --enable-gpl --enable-pthreads --disable-x86asm
  make
  make install
  ```

3.查看安装结果

```
ffmpeg -version
```



### 3、用法及命令

#### 1、查看视频信息



#### 2、视频转化



##### 视频转换

```
ffmpeg -i C:\vian\本地测试\测试视频文件\暮光之城测试片段.mp4 -b:v 1710k -r 30 -s 1280x720 -vcodec h264 -f mp4 -y C:\vian\本地测试\测试视频文件\暮光之城测试片段720P.mp4 
```

```
ffmpeg -i C:\vian\本地测试\测试视频文件\暮光之城测试片段.mp4 -b:v 490k -r 30 -s 480x360 -vcodec h264 -f mp4 -y C:\vian\本地测试\测试视频文件\暮光之城测试片段360P01.mp4 
```



```
ffmpeg -i "http://192.168.0.104:9000/apptest/course/video/twilight/%E6%9A%AE%E5%85%89%E4%B9%8B%E5%9F%8E%E6%B5%8B%E8%AF%95%E7%89%87%E6%AE%B5720P.mp4"  -b:v 490k -r 30 -s 480x360 -vcodec h264 -f mp4 -y C:\vian\本地测试\测试视频文件\暮光之城测试片段360P01.mp4 
```

视频流转本地流

```
ffmpeg -i "https://meng.wuyou-zuida.com/20200326/28491_8fa733e9/index.m3u8" -c:v copy -c:a copy C:\vian\本地测试\测试视频文件\ceshi000.flv
```



udp视频流的推送

```
ffmpeg -re -i 1.ts -c copy -f mpegts  udp://192.168.0.106:1234
```



```
ffmpeg -i "https://meng.wuyou-zuida.com/20200326/28491_8fa733e9/index.m3u8" -c:v copy -c:a copy C:\vian\本地测试\测试视频文件\ceshi000.flv
```



```
ffmpeg -i "https://d1--cn-gotcha03.bilivideo.com/live-bvc/719094/live_26250190_9200832_4000.flv?cdn=cn-gotcha03&expires=1595390099&len=0&oi=1900088630&pt=web&qn=400&trid=ce15a802b93f44bb82fe5bbcfd5da321&sigparams=cdn,expires,len,oi,pt,qn,trid&sign=7ddfd60ce519a1613bed82cd1199706b&ptype=0&src=5&level=3&platform=web&pSession=ydbZCj2s-s5DS-4mAw-MDbd-F59ZeJhW7hSJ" -c:v copy -c:a copy C:\vian\本地测试\测试视频文件\ceshi01.flv
```



##### 码率换算：

|       | 分辨率    | 帧数(fps) | 码率(Kbps) |
| ----- | --------- | --------- | ---------- |
| 1080P | 1920*1080 | 30        | 3150       |
| 720P  | 1280*720  | 30        | 1710       |
| 360P  | 480*360   | 30        | 490        |



#### 3、视频播放











##### 2倍速播放

```
ffplay -i C:\vian\本地测试\测试视频文件\暮光之城测试片段.mp4 -vf setpts=PTS/3
```

#### 4、推流

输入下面的语句即可列出电脑的设备

```
ffmpeg -list_devices true -f dshow -i dummy
```

测试摄像头是否可用

cmd中输入下面语句并回车（USB2.0 PC CAMERA为摄像头名称）

```
ffplay -f dshow -i video="USB2.0 PC CAMERA"  
```

udp视频流的推送

```
ffmpeg -re -i 暮光之城测试片段360P.mp4 -c copy -f mpegts  udp://39.105.45.130:8699
```

rtmp 推流

```
ffmpeg -re -i C:\vian\本地测试\测试视频文件\暮光之城测试片段720P.mp4 -c copy -f flv rtmp://192.168.0.104:9081/livetest/livetest
```

```
-re : 减慢帧率
-i : 指定的文件
-c : 指定音视频 copy
-f : 指定格式化的格式为flv格式
```

### 问题1：-bash: make: command not found的解决办法

Centos中无法使用make，make install，命令 make: command not found

一般出现这个-bash: make: command not found提示，

是因为安装系统的时候使用的是最小化mini安装，

系统没有安装make、vim等常用命令，直接yum安装下即可。



yum -y install gcc automake autoconf libtool make

直接ssh运行即可，安装make。

\-------------------------------------------------------

安装：

```
yum -y install gcc automake autoconf libtool make
```

安装g++:

```
yum install gcc gcc-c++
```