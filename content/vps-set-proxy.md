---
title: "云服务器代理设置"
date: 2024-11-22
location: Tianjin, CN
lat: 39.2
lon: 119.4
---

记录给云服务器设置本地局域网代理

## 大致概括
通过ssh建立本地代理服务器与云服务器的连接，开启本地代理，关闭本地代理服务器防火墙，关闭云服务器防火墙，设置云服务器代理

### 1.建立ssh连接
将本地代理端口映射至云服务器的ssh连接端口，如22，10022
```shell
#打开代理终端，进行ssh配置连接
#以本地代理端口为7897为例
ssh -i C:\Users\H\.ssh\id_rsa -R 7897:localhost:7897 username@ip -p 10022
```
确保ssh服务器连接成功
注解：其中-i 后面设置的参数是密钥对的地址，@前面是云服务器的用户名，@后面是云服务器的公网ip，-p 后面设置的是云服务器的ssh连接端口


### 2.开启本地代理
启动clash或其他代理工具，开启局域网连接，选择转发端口为7897

### 3.关闭本地代理服务器防火墙

### 4.设置云服务器的代理

```shell
#设置http代理
export http_proxy=http://localhost:7897
#设置https代理
export https_proxy=http://localhost:7897

#查看云服务代理
env | grep -i proxy

#测试代理是否成功
curl -i www.google.com
```

### 5.取消代理
```shell
export http_proxy=""
export https_proxy=""
```
