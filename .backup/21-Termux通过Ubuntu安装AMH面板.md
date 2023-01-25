[Termux通过Ubuntu安装AMH面板](https://github.com/cloudswave/blog/issues/21)

**注意先使用：uname -m查看确保手机是aarch64位架构**
# Termux下Atilo安装ubuntu
参考：https://www.jianshu.com/p/a9b30ea2ae66
# 快速安装amh面板
参考：http://amh.sh/install.htm
ubuntu下：
```
export CV=[CN] DV=N && wget http://dl.amh.sh/amh.sh && bash amh.sh
```
安装成功后系统会提示面板访问地址与AMH、MySQL默认账号密码。
常用配置目录：
```
/usr/local/amh-7.0/etc/
/usr/local/amh-7.0/vhost/
/home/wwwroot/lnmp01/vhost/
```
常用命令：
```
启动Nginx: amh nginx start
停止Nginx: amh nginx stop
重载Nginx: amh nginx reload
重启Nginx: amh nginx restart
启动MySQL: amh mysql start
停止MySQL: amh mysql stop
重载MySQL: amh mysql reload
重启MySQL: amh mysql restart
强制重载MySQL: amh mysql force-reload
启动PHP: amh php start [主标识域名]
停止PHP: amh php stop [主标识域名]
强行停止PHP: amh php force-quit [主标识域名]
重启PHP: amh php restart [主标识域名]
重载PHP: amh php reload [主标识域名]
```
# 错误处理记录
- 面板8888端口无法访问主页，amh nginx restart 看报错信息，发现ssl证书无法加载，80端口无法使用
```
nginx: [emerg] cannot load certificate "/usr/local/amh-7.0/etc/ssl/amh.crt": BIO_new_file() failed (SSL: error:02001002:system library:fopen:No such file or directory:fopen('/usr/local/amh-7.0/etc/ssl/amh.crt','r') error:2006D080:BIO routines:BIO_new_file:no such file)

nginx: [emerg] bind() to 0.0.0.0:80 failed (13: Permission denied)
```
1. 修改/usr/local/amh-7.0/vhost/amh-nginx.conf移除9999端口服务器配置部分
2. 修改/home/wwwroot/lnmp01/vhost/0.0.0.0.conf，将80端口改成1024以上比如1025