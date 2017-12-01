---
title: Hexo Blog相关
date: 2017/11/30  21:34
tags:
toc:true
---


## 文章目录

默认不开启文章目录，若要开启:
```
---
xxx: xxx
toc: true
---
```
会自动根据标题权重进行目录生成。显示在最右边。 如需要更改格式， 可去 .../yilia/layout/_partial/archive.ejs 中修改

## 文章永久链接

默认文章链结是以: http://xxx.com/2015/07/06/your-title/ 的格式的， 个人不是很喜欢这样的格式，而且末尾没有.html结尾有点动态页面的感觉，对搜索引擎是否友好也有疑问(如果你知道答案，请告诉我)，于是，我改成了 http://xxx.com/posts/programming/2016-03-18-hello-world.html 这样的格式，具体方法是在 根目录下的 _config.yml 文件里:

```
permalink: posts/:category/:year-:month-:day-:title.html
```

<!-- more -->

## CI时, tag检索问题

默认的node.js版本太低

appveyor.yml修改为

```
clone_depth: 5

environment:
  access_token:
    secure: *******************************************************
    nodejs_version: "8"

install:
  - ps: Install-Product node $env:nodejs_version
  - node --version
  - npm --version
  - npm install
  - npm install hexo-cli -g

build_script:
  - hexo generate

...
```

## CI时, npm安装失败

网络抽风, 自求多福. 
或者npm换个外国的源? 实在抽风严重就换个taobao源先试试

## CI: 在github上托管_post, 自动deploy


https://formulahendry.github.io/2016/12/04/hexo-ci/

![](http://otivusbsc.bkt.clouddn.com//579ad34f-95ba-4310-81f4-c7bbe0b29c7d)

# 参考:

https://yq.aliyun.com/articles/8607