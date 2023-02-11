[Termux通过Atilo安装Ubuntu并部署宝塔或大圣面板后运行环境不正常的解决记录](https://github.com/cloudswave/blog/issues/22)

大圣面板（高仿宝塔）：https://github.com/midoks/mdserver-web
- 无法安装nginx、php等
1. 查看任务执行日志，找错误信息
2. 宝塔各环境编译脚本路径：/www/server/panel/install/，大圣面板：/www/server/mdserver-web/plugins/，可以通过修改脚本来尝试手动本地编译
3. 通过日志或者网上搜索手动编译命令
```
# 大圣面板php7.4
# 安装所以依赖并编译
cd /www/server/mdserver-web/plugins/php/
/www/server/mdserver-web/plugins/php/versions/74/install.sh install
# 单编php
cd  /www/server/source/php/php74/
 make clean
./configure --prefix=/www/server/php/74 --exec-prefix=/www/server/php/74 --with-config-file-path=/www/server/php/74/etc --enable-mysqlnd --with-mysqli=mysqlnd --with-pdo-mysql=mysqlnd --with-zlib-dir=/www/server/lib/zlib --with-zip --enable-ftp --enable-mbstring --enable-sockets --enable-simplexml --enable-soap --enable-posix --enable-sysvmsg --enable-sysvsem --enable-sysvshm --disable-intl --disable-fileinfo --without-iconv --with-curl --with-libdir=lib64 --enable-fpm
make && make install
```

```
# 大圣面板php5.6
# 安装所以依赖并编译
cd /www/server/mdserver-web/plugins/php/
/www/server/mdserver-web/plugins/php/versions/56/install.sh install
# 单编php
cd  /www/server/source/php/php56/
./configure -prefix=/www/server/php/56 --exec-prefix=/www/server/php/56 --with-config-file-path=/www/server/php/56/etc --with-zlib-dir=/www/server/lib/zlib --enable-mysqlnd --with-mysql=mysqlnd --with-pdo-mysql=mysqlnd --with-mysqli=mysqlnd --enable-zip --enable-mbstring --enable-simplexml --enable-ftp --enable-sockets --enable-pcntl --enable-shmop --enable-soap --enable-posix --enable-sysvmsg --enable-sysvsem --enable-sysvshm --disable-intl --disable-fileinfo --without-iconv --with-curl --with-libdir=lib64 --enable-fpm
make && make install
可能由于手机内存不足，上面编译zend会失败

方案2: 通过ln关联已安装的php环境并配置
ln -s /usr/local/php-generic-7.4 /www/server/php/74
cd /usr/local/php-generic-7.4/etc/
cp php-fpm.conf.default php-fpm.conf
cd php-fpm.d
cp www.conf.default www.conf
vi www.conf # 替换listen = 127.0.0.1:9000 为 listen = /tmp/php-cgi-74.sock

# 增加enable-php-74.conf、fastcgi.conf、pathinfo.conf
vi /www/server/web_conf/php/conf/enable-php-74.conf
location ~ [^/]\.php(/|$)
{
	try_files $uri =404;
	fastcgi_pass  unix:/tmp/php-cgi-74.sock;
	fastcgi_index index.php;
	include /www/server/web_conf/php/conf/fastcgi.conf;
	include /www/server/web_conf/php/conf/pathinfo.conf;
}

vi /www/server/web_conf/php/conf/fastcgi.conf
fastcgi_param  SCRIPT_FILENAME    $document_root$fastcgi_script_name;
fastcgi_param  QUERY_STRING       $query_string;
fastcgi_param  REQUEST_METHOD     $request_method;
fastcgi_param  CONTENT_TYPE       $content_type;
fastcgi_param  CONTENT_LENGTH     $content_length;

fastcgi_param  SCRIPT_NAME        $fastcgi_script_name;
fastcgi_param  REQUEST_URI        $request_uri;
fastcgi_param  DOCUMENT_URI       $document_uri;
fastcgi_param  DOCUMENT_ROOT      $document_root;
fastcgi_param  SERVER_PROTOCOL    $server_protocol;
fastcgi_param  REQUEST_SCHEME     $scheme;
fastcgi_param  HTTPS              $https if_not_empty;

fastcgi_param  GATEWAY_INTERFACE  CGI/1.1;
fastcgi_param  SERVER_SOFTWARE    nginx/$nginx_version;

fastcgi_param  REMOTE_ADDR        $remote_addr;
fastcgi_param  REMOTE_PORT        $remote_port;
fastcgi_param  SERVER_ADDR        $server_addr;
fastcgi_param  SERVER_PORT        $server_port;
fastcgi_param  SERVER_NAME        $server_name;

# PHP only, required if PHP was built with --enable-force-cgi-redirect
fastcgi_param  REDIRECT_STATUS    200;


vi /www/server/web_conf/php/conf/pathinfo.conf
set $real_script_name $fastcgi_script_name;
if ($fastcgi_script_name ~ "^(.+?\.php)(/.+)$") {
        set $real_script_name $1;
        set $path_info $2;
}
fastcgi_param SCRIPT_FILENAME $document_root$real_script_name;
fastcgi_param SCRIPT_NAME $real_script_name;

# 启动nginx php-fpm
/www/server/openresty/nginx/sbin/nginx
php-fpm
```
宝塔面板：
手动编译php7.3
```
bash /www/server/panel/install/php.sh install 7.3
```

参考文章手动编译nginx
[宝塔自定义编译安装 Nginx 并配置 TLS1.3](https://www.nange.cn/tls13-for-nginx.html)
[宝塔Nginx自编译](https://blog.csdn.net/nx_xn/article/details/110120525)
[宝塔环境编译安装第三方PHP扩展示例](https://www.landui.com/help/show-7948.html)
```
宝塔手动编译集成nginx1.20记录：
如果存在nginx可以使用/www/server/nginx/sbin/nginx -V查看configure参数，并备份nginx
root@localhost:/www/server/nginx#curl -O http://nginx.org/download/nginx-1.20.0.tar.gz
root@localhost:/www/server/nginx# tar -zxvf nginx-1.20.0.tar.gz
root@localhost:/www/server/nginx#cd nginx-1.20.0/
root@localhost:/www/server/nginx/nginx-1.20.0#./configure --user=www --group=www --prefix=/www/server/nginx
root@localhost:/www/server/nginx/nginx-1.20.0#make && make install

修改/www/server/nginx/conf/nginx.conf
nginx验证命令
/www/server/nginx/sbin/nginx -t
手动启动nginx
/www/server/nginx/sbin/nginx
```
/www/server/nginx/conf/nginx.conf:
```

#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;
  
    server_names_hash_bucket_size 512; # 新增避免nginx名称过长报错
    client_header_buffer_size 32k;
    large_client_header_buffers 4 32k;
    client_body_buffer_size 50m;
    client_max_body_size 50m;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       1025;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
  
  include /www/server/panel/vhost/nginx/*.conf; # 新增使其支持宝塔的多网站

}

```
# 问题记录
- 运行内存不足导致编译失败，cc: fatal error: Killed signal terminated program cc1
网络搜索都说可以增加虚拟内存大小[参考](https://www.cnblogs.com/huojing/articles/15824136.html)，但是在termux中的ubuntu中以下方法会报错：swapon failed Operation not permitted。
```
#获取要增加的2G的SWAP文件块
dd if=/dev/zero of=/swapfile bs=1k count=2048000
#创建SWAP文件
mkswap /swapfile 
#激活SWAP文件
swapon /swapfile   
#查看SWAP信息是否正确
swapon -s  
#添加到fstab文件中让系统引导时自动启动
```
方案2: 尝试清理进程，关闭面板，再手动编译，实验中..

- 大圣面板新建静态站点openresty nginx未启动
```
使用以下命令测试nginx
ps -ef | grep nginx
/www/server/openresty/nginx/sbin/nginx -t
发现使用了80端口，termux需要使用1024以上端口，故修改成1026
vi /www/server/openresty/nginx/conf
80->1026
继续测试nginx
/www/server/openresty/nginx/sbin/nginx -t
手动启动nginx
/www/server/openresty/nginx/sbin/nginx
验证网页
curl http://127.0.0.1:1026
```
测试发现：宝塔的nginx同样无法启动，也需要如此验证，nginx路径：/www/server/nginx/
- 宝塔报错：ulimit: open files: cannot modify limit: Operation not permitted
寻找并修改源码
```
root@localhost:/www/server# grep -rn "ulimit"
Binary file panel/data/default.db matches
panel/class/public.py:299:        result = ExecShell("ulimit -n 10240 && /www/server/nginx/sbin/nginx -t -c /www/server/nginx/conf/nginx.conf");
panel/class/public.py:302:        result = ExecShell("ulimit -n 10240 && /www/server/apache/bin/apachectl -t");
panel/class/system.py:533:            result = public.ExecShell('ulimit -n 10240 && ' + self.setupPath+'/apache/bin/apachectl -t');
panel/class/system.py:551:            result = public.ExecShell('ulimit -n 10240 && nginx -t -c '+self.setupPath+'/nginx/conf/nginx.conf');
Binary file panel/class/public.pyc matches
Binary file panel/class/system.pyc matches
root@localhost:/www/server#
```
修改panel/class/public.py和panel/class/system.py将ulimit -n 10240删除:
```
    if get_webserver() == 'nginx':
        result = ExecShell("/www/server/nginx/sbin/nginx -t -c /www/server/nginx/conf/nginx.conf");
        searchStr = 'successful'
    else:
        result = ExecShell("/www/server/apache/bin/apachectl -t");
```
重启宝塔：/etc/init.d/bt restart