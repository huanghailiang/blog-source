---
title: 建立自己的github博客
date: 2017-08-15 10:06
tags: 搭建自己的github博客
---

# 可以根据博客中的hello-world文件中讲述的安装

# 下面是一些详细的步骤
1、安装node,可以去node的官网直接下载 https://nodejs.org/en/ （这里我下载的是node-v6.11.1-x64）
2、安装hexo，可以打开cmd终端输入

    npm install -g hexo-cli
<!--more-->
3、安装完hexo后开始在电脑上建立一下自己写博客的文件夹，依旧是在cmd终端中运行

    cd C:\Users\Darren\Documents     #实际上是进入了电脑的文档文件夹
    mkdir blog
    cd blog
    hexo init blog
    cd blog
    hexo s
    然后就可以用 http://localhost:4000/ 来查看博客是否安装成功
    npm install hexo-deployer-git --save
4、修改_config.yml这个配置文件


    # Site
    title: Darren
    subtitle: 海亮的笔记本
    description:
    author: Darren
    language: zh-Hans
    timezone:

    
    # Deployment
    ## Docs: https://hexo.io/docs/deployment.html
    deploy:
      type: git
      repo: 

repo是自己在自己github中建立的地址

5、开始部署

    hexo d

之后可以去看一下你的博客就好了

https://huanghailiang.github.io/

到此博客基本搭建完成了，剩下的东西就可以按照自己的兴趣去自己配置了。

# 如果标签没有的话需要自己新建一个

    hexo new page tags
    
    里面修改为：
    
    ---
    title: tags
    date: 2017-08-15 11:06:32
    type: tags
    ---