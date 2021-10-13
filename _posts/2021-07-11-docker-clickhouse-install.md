---
layout:     post
title:      Docker环境下安装Clickhouse，Clickhouse初体验
subtitle:   Docker-clickhouse-install
date:       2021-07-11
author:     Frank
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - 大数据
    - Clickhouse
---
# Docker-clickhouse-install

## 安装步骤

安装docker
安装clickhouse server
运行临时clickhouse导出配置文件
初始化配置&账号
运行clickhouse server
安装windows clickhouse客户端连接软件

## 详细步骤

1、安装docker
+ Docker Desktop
[https://www.docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop "安装docker桌面版本")

2、安装clickhouse server
```
docker pull yandex/clickhouse-server
docker pull yandex/clickhouse-client
```

3、运行临时clickhouse导出配置文件
```
docker run --rm -d --name=temp-clickhouse-server yandex/clickhouse-server
```

4、初始化配置
```
mkdir D:/clickhouse/conf
mkdir D:/clickhouse/data
mkdir D:/clickhouse/log
```

5、初始化配置&账号
```
docker cp temp-clickhouse-server:/etc/clickhouse-server/config.xml D:/clickhouse/conf/config.xml
docker cp temp-clickhouse-server:/etc/clickhouse-server/users.xml D:/clickhouse/conf/users.xml

docker exec -it temp-clickhouse-server /bin/bash
PASSWORD=$(base64 < /dev/urandom | head -c8); echo "zhai"; echo -n "zhai" | sha256sum | tr -d '-'
-- 修改 D:/clickhouse/conf/config.xml 文件
<listen_host>0.0.0.0</listen_host>

docker stop temp-clickhouse-server
```

6、运行clickhouse server
```
docker run -d --name=single-clickhouse-server -p 8123:8123 -p 9000:9000 -p 9009:9009 --ulimit nofile=262144:262144 --volume D:/clickhouse/data:/var/lib/clickhouse:rw --volume D:/clickhouse/conf:/etc/clickhouse-server:rw --volume D:/clickhouse/log:/var/log/clickhouse-server:rw yandex/clickhouse-server
```
7、安装windows clickhouse客户端连接软件
+ 安装DBeaver社区版
[https://dbeaver.io/files/dbeaver-ce-latest-x86_64-setup.exe](https://dbeaver.io/files/dbeaver-ce-latest-x86_64-setup.exe "安装DBeaver社区版")