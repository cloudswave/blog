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

## 在`github`或者`gitcafe`中添加page项目，**项目名和用户名相同**

 ![图片](https://dn-coding-net-production-pp.qbox.me/73623394-250b-4454-bf70-2e6bdfbaa6fb.png) 

配置`_config.yml`, github或者gitcafe部署

```
deploy: 
  type: git
  repository: git@gitcafe.com:ethanzhu/ethanzhu.git
  branch: gitcafe-pages
# deploy: 
#   type: git
#   repository: https://github.com/cloudswave/cloudswave.github.io.git
#   branch: master
```

## 发布到`github`或者`gitcafe`中

```
  hexo d
```
  