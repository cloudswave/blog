title: 利用日本免费Docker搭建个人SS服务
date: 2017-07-11 23:36:50
tags: [ss,docker]
---
新建应用

## 第一步

登录进入后台，点击”Create a new application” 新建应用。

## 第二步

按照以下配置：
```
App Name： 自定义
Image：lowid/ss-with-net-speeder:latest
Memor：512M
Port:  1111
CMD:   -p 1111 -k 密码 -m aes-256-cfb
#其他的默认即可
```
如图所示

 ![图片](https://dn-coding-net-production-pp.qbox.me/1f98686c-25e5-4d2b-8c60-641bb4e67cc3.png) 


## 第三步

等待启动完成后，点击应用名进入，获得SS登录信息。
配置arukas_io查看SS登录信息

 ![图片](https://dn-coding-net-production-pp.qbox.me/0ec93b7f-469d-4617-9315-fd80accd02ba.png) 


测试速度

由于是日本直连大陆，速度很可观。

