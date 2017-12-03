---
title: Hexo Blog相关
date: 2017/11/30  21:34
tags:
---

## SEO

http://www.jianshu.com/p/efaf72aab32e?utm_campaign=maleskine&utm_content=note&utm_medium=seo_notes&utm_source=recommendation


## CI时, npm安装失败

网络抽风, 自求多福. 
或者npm换个外国的源? 实在抽风严重就换个taobao源先试试

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


## CI: 在github上托管_post, 自动deploy


https://formulahendry.github.io/2016/12/04/hexo-ci/

![](http://otivusbsc.bkt.clouddn.com//579ad34f-95ba-4310-81f4-c7bbe0b29c7d)

# 参考:

https://yq.aliyun.com/articles/8607