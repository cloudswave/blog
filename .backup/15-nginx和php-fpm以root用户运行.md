[nginx和php-fpm以root用户运行](https://github.com/cloudswave/blog/issues/15)

**开启root权限，请谨慎使用！！！**
为了使KodExplorer能读写root用户数据需要修改nginx，php-fpm运行用户为root，下面为修改笔记
- nginx version: nginx/1.18.0 (Ubuntu)
- PHP 8.1

sudo vi /etc/nginx/nginx.conf
```
user root; #第一行
...
```

sudo vi /etc/php/8.1/fpm/pool.d/www.conf
```
...
# 修改两处
listen.owner = root
listen.group = root
...
user = root
group = root
```

sudo vi /lib/systemd/system/php8.1-fpm.service
```
# 加上-R允许root执行
...
ExecStart=/usr/sbin/php-fpm8.1 -R --nodaemonize --fpm-config /etc/php/8.1/fpm/php-fpm.conf
...
````

防火墙问题可能导致80端口无法访问
```
# 如果外网无法访问请关闭防火墙或者添加规则
service iptables stop 
iptables -I INPUT -p tcp --dport 80 -j ACCEPT
```

重启服务
```
systemctl daemon-reload # php8.1-fpm.service修改刷新
/etc/init.d/php8.1-fpm restart
/etc/init.d/nginx restart

# check php8.1-fpm.sock是否生成
ls /run/php/
```
<!--csdn-article-id:128278703-->