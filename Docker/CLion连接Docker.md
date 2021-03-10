# CLion连接Docker

## 安装Docker



## 创建配置文件

在CLion的新建项目下创建`docker`文件夹

我的文件目录如下：



### 创建`Dockerfile`文件

写入代码


```dockerfile
FROM ubuntu:18.04

ENV DEBIAN_FRONTEND=noninteractive

# 更换ubuntu的源
RUN echo "deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse deb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse"> /etc/apt/sources.list

RUN apt-get update

# 安装GCC环境
RUN apt-get install -y build-essential
RUN apt-get install -y cmake
RUN apt-get install -y gdb

RUN apt-get install -y openssh-server
RUN mkdir /var/run/sshd
RUN sed -ri 's/^PermitRootLogin\s+.*/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN sed -ri 's/UsePAM yes/#UsePAM yes/g' /etc/ssh/sshd_config

RUN apt-get install -y rsync
RUN sed -ri 's/RSYNC_ENABLE=false/RSYNC_ENABLE=true/g' /etc/default/rsync
COPY rsync.conf /etc

RUN echo 'root:000000' |chpasswd

RUN mkdir /root/sync

COPY entrypoint.sh /sbin
RUN chmod +x /sbin/entrypoint.sh
ENTRYPOINT [ "/sbin/entrypoint.sh" ]
```

先不要急着运行，还有文件需要创建

### 在同级目录下创建`entrypoint.sh`和`rsync.conf`

#### `rsync.conf`

`rsync`是用来对文件进行同步的，没有这个的话两边无法交互文件

```
max connections = 8
log file = /var/log/rsync.log
timeout = 300

[sync]
comment = sync
# 你需要同步的文件夹
path = /root/sync
read only = no
list = yes
uid = root
gid = root
```

#### `entrypoint.sh`

```sh
#!/bin/bash

/usr/bin/rsync --daemon --config=/etc/rsync.conf
/usr/sbin/sshd -D
```

### 创建`docker-compose.yml`文件

```yml
version: "3"

services:
  env:
  # 目标镜像
    image: clion/remote-cpp-env:1.0 
    # 运行的镜像名
    container_name: webSever
    # 端口（放下面解释）
    ports:
      - "2222:22"
      - "8730:873"
    cap_add:
      - ALL
```

- `2222:22`（`SSH`）：由于本地端口会起冲突，所以用`2222`端口代替
- `8730:873`（`rsync`）：同理

## 运行

`build`命令：

```
docker build -t clion/remote-cpp-env:1.0 -f Dockerfile .
```

- `clion/remote-cpp-env:0.5`：是你要生成镜像的**<u>名字</u>**与<u>**版本**</u>

- `Dockerfile`：是你之前写的`dockerfile`的文件名
- 最后的一个`.`时表示在当前目录下

`docker-compose`命令:

```
docker-compose up
# 后台执行
docker-compose up -d
```

