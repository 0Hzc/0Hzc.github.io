---
title: "如何再次部署yjs+quill协同编辑器"
date: 2024-11-20
location: Tianjin, CN
lat: 39.2
lon: 119.4
---

本文记录如何使用yjs+quill协同编辑器进行二次部署,使用已经搭建过的yjsdemo实现快速部署
用于服务器更新资源，需要二次部署时使用


### 步骤1：
从github仓库中下载源码

### 步骤2：
安装npm、nginx反向代理

### 步骤3：
在工程目录下npm install

### 步骤4：
编辑nginx配置文件,以petherfish.cn为例

```shell
sudo nano /etc/nginx/sites-available/petherfish.cn
```
配置文件如下：
```shell
server {
    listen 80;
    listen [::]:80;
    server_name petherfish.cn;

    # 编辑器的反向代理配置
    location /edit {
        proxy_pass http://localhost:5173;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # WebSocket 支持
        proxy_read_timeout 86400;
    }

    # WebSocket 服务器的反向代理配置
    location /ws {
        proxy_pass http://localhost:1234;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_read_timeout 86400;
    }

    # 用于处理其他请求
    location / {
        root /usr/share/nginx/html;
        index index.html index.htm;
        try_files $uri $uri/ =404;
    }

    # 日志配置
    access_log /var/log/nginx/petherfish.cn.access.log;
    error_log /var/log/nginx/petherfish.cn.error.log;
}
```
重启nginx
```shell
sudo systemctl restart nginx
```


### 步骤5：
本地测试
```shell
npm start
```

```shell
npm run dev
```
无报错后访问petherfish.cn/edit/,测试是否可以正常访问

### 步骤6：
编写vite.config.js文件，系统服务文件
编写webserver.py文件，系统服务文件