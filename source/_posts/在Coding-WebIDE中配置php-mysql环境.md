title: 在Coding WebIDE中配置php+mysql环境
date: 2015-09-16 11:56:25
tags: [php,mysql,coding,WebIDE]
---
WebIDE:数据库mysql默认密码：coding

设置服务器访问权限
修改配置apache配置文件：
> sudo vim /etc/apache2/apache2.conf   

修改

```
  <Directory /> 
        Options FollowSymLinks 
        AllowOverride None 
        Require all granted 
   </Directory> 
```

修改Document路径               
> sudo vim /etc/apache2/sites-available/000-default.conf

修改为

> /home/coding/workspace


apache启动： 
> sudo apache2ctl start    
  sudo apache2ctl restart 


目录权限：
> chmod -R 777 /home/coding/workspace/*

安装gd库：

> sudo apt-get install php5-gd 
  sudo /etc/init.d/apache2 restart