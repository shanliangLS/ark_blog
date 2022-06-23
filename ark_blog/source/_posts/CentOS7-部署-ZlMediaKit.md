---
title: CentOS7 部署 ZlMediaKit
date: 2022-06-02 ‏‎09:38:00
categories:
- 快速上手
- 操作系统
tags:
- Linux
- ZlMediaKit
---

[官网](https://github.com/ZLMediaKit/ZLMediaKit/wiki/%E5%BF%AB%E9%80%9F%E5%BC%80%E5%A7%8B)

## 获取代码

```bash
#国内用户推荐从同步镜像网站gitee下载 
git clone --depth 1 https://gitee.com/xia-chu/ZLMediaKit
cd ZLMediaKit
#千万不要忘记执行这句命令
git submodule update --init
```

## 编译器

要求 gcc 版本 >= 4.8(4.7应该也能支持)

安装编译器

```bash
sudo yum -y install gcc
sudo yum -y install gcc-c++
```

## cmake

```bash
sudo yum -y install cmake
```

我安装完后还是 2.8.12.2 版本，不够：`CMake 3.0.0 or higher is required. You are running version 2.8.12.2`

```bash
[root@liangshan build]# cmake -version
cmake version 2.8.12.2
```

[升级 cmake](./CentOS7-升级-cmake.md)

## 依赖库

ZLMediaKit 可选依赖一些第三方库，这些库都不是必选的

- openssl  
flash player 在播放 rtmp 时，采用的是复杂握手模式，如果不安装该库，flash player 将播放不了 zlmediakit 提供的 rtmp url  
同时 ZLMediaKit 的 https/rtsps 相关功能需要使用 openssl 才能开启
- ffmpeg  
zlmediakit 可以通过 fork ffmpeg 进程的方式实现多种协议的拉流，编译时不需要安装 FFmpeg
- sdl、avcodec、avutil  
这3个库供 ZLMediaKit 的 test_player 测试程序使用，你通常不需要安装这3个库

### openssl

```bash
yum install openssl
yum install openssl-devel
```

### ffmpeg

参考 [centos7：安装ffmpeg](https://www.jianshu.com/p/7bf6385eee6e)

```bash
# 升级yum
yum install epel-release -y
yum update -y

# 安装Nux Dextop Yum 源
sudo rpm --import http://li.nux.ro/download/nux/RPM-GPG-KEY-nux.ro
sudo rpm -Uvh http://li.nux.ro/download/nux/dextop/el7/x86_64/nux-dextop-release-0-5.el7.nux.noarch.rpm

# 安装FFmpeg和FFmpeg开发包
sudo yum install ffmpeg ffmpeg-devel -y

# 测试安装
ffmpeg --help
```

## 构建和编译项目

由于开启 webrtc 相关功能比较复杂，默认是不开启编译的，如果你对 zlmediakit 的 webrtc 功能比较感兴趣，可以参考[这里](https://github.com/ZLMediaKit/ZLMediaKit/wiki/zlm%E5%90%AF%E7%94%A8webrtc%E7%BC%96%E8%AF%91%E6%8C%87%E5%8D%97)

```bash
cd ZLMediaKit
mkdir build
cd build
cmake ..
make -j4
```

## 运行

ZLMediaKit 工程主要生成3种二进制目标文件，他们的生成的路径在 release 目录下，这些目标文件主要分为

- MediaServer 进程

这是 ZLMediaKit 作为服务器的主进程，该进程可以在免去开发的情况下直接作为测试流媒体服务器使用，如果你需要更复杂的业务逻辑，可以通过 [Web HOOK](https://github.com/zlmediakit/ZLMediaKit/wiki/MediaServer%E6%94%AF%E6%8C%81%E7%9A%84HTTP-HOOK-API) 和 [RESTful API](https://github.com/zlmediakit/ZLMediaKit/wiki/MediaServer%E6%94%AF%E6%8C%81%E7%9A%84HTTP-API) 实现，同时你可以通过[配置文件](https://github.com/zlmediakit/ZLMediaKit/wiki/%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E8%AF%A6%E8%A7%A3)控制其参数

```bash
cd ZLMediaKit/release/linux/Debug
#通过-h可以了解启动参数
./MediaServer -h
#以守护进程模式启动
./MediaServer -d &
```

通过这种方式启动的，使用 `killall -2 MediaServer` 以便优雅关闭服务器(程序收到SIGINT信号后会自动释放资源并退出)

- c api 的SDK

ZLMediaKit 也提供 c 的 api 二次开发 sdk 库，头文件在 `ZLMediaKit/api/include`，库文件为

```bash
ZLMediaKit/release/linux/Debug/libmk_api.so
```

- 以 `test_` 开头的测试程序

相关代码在 `ZLMediaKit/tests` 目录下，你可以对照代码启动测试进程

## 测试

### 推流测试

ZLMediaKit 支持 rtsp/rtmp/rtp 推流，一般通常使用 obs/ffmpeg 推流测试，其中 FFmpeg 推流命令支持以下

- 使用 rtsp 方式推流

```bash
# h264推流
ffmpeg -re -i "/path/to/test.mp4" -vcodec h264 -acodec aac -f rtsp -rtsp_transport tcp rtsp://127.0.0.1/live/test
# h265推流
ffmpeg -re -i "/path/to/test.mp4" -vcodec h265 -acodec aac -f rtsp -rtsp_transport tcp rtsp://127.0.0.1/live/test
```

如果报错

```bash
The encoder 'aac' is experimental but experimental codecs are not enabled, add '-strict -2' if you want to use it
```

需要在最后一个参数前加 `-strict -2`，如

```bash
ffmpeg -re -i "/path/to/test.mp4" -vcodec h264 -acodec aac -f rtsp -rtsp_transport tcp -strict -2 rtsp://127.0.0.1/live/test
```

- 使用 rtmp 方式推流

```bash
#如果未安装FFmpeg，你也可以用obs推流
ffmpeg -re -i "/path/to/test.mp4" -vcodec h264 -acodec aac -f flv rtmp://127.0.0.1/live/test
# RTMP标准不支持H265,但是国内有自行扩展的，如果你想让FFmpeg支持RTMP-H265,请按照此文章编译：https://github.com/ksvc/FFmpeg/wiki/hevcpush
```

- 使用 rtp 方式推流

```bash
# h264推流
ffmpeg -re -i "/path/to/test.mp4" -vcodec h264 -acodec aac -f rtp_mpegts rtp://127.0.0.1:10000
# h265推流
ffmpeg -re -i "/path/to/test.mp4" -vcodec h265 -acodec aac -f rtp_mpegts rtp://127.0.0.1:10000
```

### 观察日志

如果推流成功，会打印这种日志，日志中相关字符串分别代表：

```bash
2020-04-10 12:51:52.331 I | regist rtsp __defaultVhost__ rtp 206442D7
                                    ^           ^         ^      ^
                                  schema      vhost      app stream_id
```
