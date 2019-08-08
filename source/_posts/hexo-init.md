---
title: hexo_init
date: 2019-08-08 15:00:54
tags: hexo
categories:
description: hexo 环境搭建
---

## # 搭建环境：

- 下载node
- install git
- npm --version
- npm install -g hexo-cli

## # 初始化：
- hexo init {your blog name}
- cd hexo {your blog name}
- npm install
- hexo server `本地运行查看`

## # 配置：

```yml
# Site
title: xxx
subtitle:
description: xxx
keywords:
author: xxx
language: zh-Hans  
timezone:

# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: https://xxxx.github.io/xxxx/ {你要部署仓库的Page地址}
root: /xxxx/
permalink: :year/:month/:day/:title/
permalink_defaults:

# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: next


# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repository: https://github.com/xxxx/xxxx.git
  branch: master
```

```json
{
  "name": "hexo-site",
  "version": "0.0.0",
  "private": true,
  "hexo": {
    "version": "3.9.0"
  },
  "dependencies": {
    "hexo": "^3.9.0",
    "hexo-asset-image": "0.0.3",
    "hexo-generator-archive": "^0.1.5",
    "hexo-generator-category": "^0.1.3",
    "hexo-generator-index": "^0.2.1",
    "hexo-generator-tag": "^0.2.0",
    "hexo-renderer-ejs": "^0.3.1",
    "hexo-renderer-stylus": "^0.3.3",
    "hexo-renderer-marked": "^1.0.1",
    "hexo-deployer-git": "^0.3.1", //git 包
    "hexo-server": "^0.3.3"
  }
}
```

## # hexo 命令
- 编译 hexo g
- 清除 hexo clean
- 运行 hexo s
- 提交仓库 hexo d