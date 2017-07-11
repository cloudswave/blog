title: coding WebIDE 安装hexo
date: 2015-09-15 21:27:44
tags: [WebIDE,Hexo,Github,gitcafe]
---
## 控制台下执行以下命令：

```
  $ npm install -g Hexo #安装
  $ hexo init
  $ npm install
  $ hexo g #生成静态文件
  $ hexo s #启动服务器
```

WebIDE预览链接
 ![t](https://dn-coding-net-production-pp.qbox.me/9278520a-aff1-40c9-9fdf-39dfd5bbcd60.png) 

## 在`github`和`coding`中添加page项目，**项目名和用户名相同**，coding中记得开通pages服务

配置`_config.yml`, github和coding部署

```
deploy: 
  type: git
  repository: 
    github: https://github.com/cloudswave/cloudswave.github.io.git,master
    coding: https://git.coding.net/ethanzhu/ethanzhu.coding.me.git,master
# deploy: 
#   type: git
#   repository: https://github.com/cloudswave/cloudswave.github.io.git
#   branch: master
```

## 发布到`github`和`coding`中

```
  hexo d
```
  