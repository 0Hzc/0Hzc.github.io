---
title: "使用github+hugo部署博客"
date: 2024-11-07
location: Tianjin, CN
lat: 39.2
lon: 119.4
---

本文记录如何使用github的两个仓库进行部署hugo生成的静态html博客

## 流程
### 步骤1：
在github上建立一个私有库，用于存放hugo的源代码（后续记录如何本地安装并调试hugo进行网页效果展示）

### 步骤2：
再建立另一个仓库：0hzc.github.io，此仓库命名必须命名为xxx.github.io，xxx为你的github用户名

### 步骤3：
在本地调试完hugo的网页确定无误后，在目录下新建.github文件夹，此文件夹与hugo new site 的文件夹同级,再在.github文件夹下新建一个名为workflows的文件夹

### 步骤4：
在.github/workflows文件夹下新建xxx.yml文件，xxx随意命名，此文件为githubwebpages的自动部署文件，内容可填写为如下：
```shell
name: github pages # 名字自取

on:
  push:
    branches:
      - main  # 这里的意思是当 main分支发生push的时候，运行下面的jobs，这里先改为github-actions

jobs:
  deploy: # 任务名自取
    runs-on: ubuntu-latest	# 在什么环境运行任务
    steps:
      - uses: actions/checkout@v2	# 引用actions/checkout这个action，与所在的github仓库同名
        with:
          submodules: true  # Fetch Hugo themes (true OR recursive) 获取submodule主题
          fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod

      - name: Setup Hugo	# 步骤名自取
        uses: peaceiris/actions-hugo@v2	# hugo官方提供的action，用于在任务环境中获取hugo
        with:
          hugo-version: 'latest'	# 获取最新版本的hugo
          extended: true

      - name: Build
        run: hugo --minify	# 使用hugo构建静态网页

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3	# 一个自动发布github pages的action
        with:
          # github_token: ${{ secrets.GITHUB_TOKEN }} 该项适用于发布到源码相同repo的情况，不能用于发布到其他repo
          external_repository: 0Hzc/0Hzc.github.io	# 发布到哪个repo
          personal_token: ${{ secrets.hzcblog }}	# 发布到其他repo需要提供上面生成的personal access token
          publish_dir: ./public	# 注意这里指的是要发布哪个文件夹的内容，而不是指发布到目的仓库的什么位置，因为hugo默认生成静态网页到public文件夹，所以这里发布public文件夹里的内容
          publish_branch: main	# 发布到哪个branch

```
其中需要做修改的为“ external_repository: 0Hzc/0Hzc.github.io”需要将对应的部分改为步骤2的仓库名，“personal_token: ${{ secrets.hzcblog }}”需要将对应的部分改为生成的personal access token

### 步骤5：
使用git工具推送至步骤一建立的仓库即可

## git的相关使用
```shell
#将本地文件进行git
git init

#连接远程仓库
git  remote add  origin xxxxx   #origin是远程仓库的别名，可以自定义，后续推送至仓库时需要用到这个别名，xxxx是远程仓库的url

#检查连接的远程仓库，以及在本地的别名
git remote -v

#将文件/修改的文件，添加至暂存区
git add .    #也可以指定文件添加  将.改为具体文件路径

#可查看暂存区有哪些文件
git status

#确定暂存区无误，确定提交
git commit -m "提交信息"  #提交信息为自定义，可以写本次提交的说明

#将暂存区的文件提交至远程仓库
git push origin main  #origin是远程仓库的别名，main是远程仓库的分支（branch），也可以自定义

#删除本地与远程仓库的连接
git remote remove origin #origin是远程仓库在本地的别名
```

## 参考链接
https://krislinzhao.github.io/docs/create-a-wesite-using-github-pages-and-hugo/