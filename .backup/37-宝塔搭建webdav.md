[宝塔搭建webdav](https://github.com/cloudswave/blog/issues/37)

## 方法1:宝塔直接编译nginx搭建webdav
选择安装nginx的时候选择编译安装并且添加自定义模块，目的是补全nginx的webdav功能
模块名称：
http_dav_module
模块描述：
webdev
模块参数：
--with-http_dav_module --add-module=/root/nginx-dav-ext-module
前置脚本：
git clone https://github.com/arut/nginx-dav-ext-module.git /root/nginx-dav-ext-module
新建网页，自己命名，php版本选择纯静态并且在配置文件中添加
```
location / {
        root /www/wwwroot/webdav;#这里写你webdav的路径
        #client_max_body_size 102400M;#大文件支持
        autoindex on;
        dav_methods PUT DELETE MKCOL COPY MOVE;
        # 需要 nginx-dav-ext-module 才有下面的选项
        dav_ext_methods PROPFIND OPTIONS LOCK UNLOCK;
        create_full_put_path  on;
    }
```
然后再网站目录添加账号密码即可，如果需要用到webdav的话，账号就是你添加的账号，密码就是你添加的密码，地址就是你的webdav的网页。

**注意：如果编译安装失败，在不勾选添加自定义模块后再次编译安装试一试。如果仍然失败可以尝试方法2。**

## 方法2: 手动nginx编译webdav模块并替换
1，nighx必须是编译安装（安装过的卸载重新安装）
2，通过ps命令查看nginx文件的路径
ps -elf | grep nginx
得到宝塔面板nginx的具体路径：/www/server/nginx/sbin/nginx
3，执行：
```
cd /www/server/nginx/sbin/   #进入到路径
cp nginx nginx.bak    #备份nginx
```
4，下载nginx-dav-ext-module
git clone https://github.com/arut/nginx-dav-ext-module.git
5，拷贝至src目录
```
cp -R ./nginx-dav-ext-module /www/server/nginx/src
```
6，nignx-V（V大写）
```
cd /www/server/nginx/src
nginx -V
```
查看nignx安装了哪些模块
然后全部复制到一个记事本中备用。然后在最后面添加:
--with-http_dav_module --add-module=/www/server/nginx/src/nginx-dav-ext-module
7，编译（以下代码为1.17）
```
./configure --prefix=/www/server/nginx --add-module=/www/server/nginx/src/ngx_devel_kit --add-module=/www/server/nginx/src/lua_nginx_module --add-module=/www/server/nginx/src/ngx_cache_purge --add-module=/www/server/nginx/src/nginx-sticky-module --with-openssl=/www/server/nginx/src/openssl --with-pcre=pcre-8.43 --with-http_v2_module --with-stream --with-stream_ssl_module --with-http_stub_status_module --with-http_ssl_module --with-http_image_filter_module --with-http_gzip_static_module --with-http_gunzip_module --with-ipv6 --with-http_sub_module --with-http_flv_module --with-http_addition_module --with-http_realip_module --with-http_mp4_module --with-ld-opt=-Wl,-E --with-cc-opt=-Wno-error --with-ld-opt=-ljemalloc --with-http_dav_module --add-module=/www/server/nginx/src/nginx-dav-ext-module
make
```
注：需要进入到：/www/server/nginx/src编译才会有效。

8，nginx替换
```
rm -rf /www/server/nginx/sbin/nginx
cp objs/nginx /www/server/nginx/sbin/
service nginx restart
```
9，检查安装
使用nginx -V，如果最后出现 --with-http_dav_module --add-module=/www/server/nginx/src/nginx-dav-ext-module即为成功

10，配置网站

新建静态站点，在配置中输入以下代码
```
client_max_body_size 102400M;#大文件支持

location / {
        root /www/wwwroot/你的域名;
                autoindex on;
        dav_methods PUT DELETE MKCOL COPY MOVE;
        # 需要 nginx-dav-ext-module 才有下面的选项
        dav_ext_methods PROPFIND OPTIONS LOCK UNLOCK;
        create_full_put_path  on;
    }
```
11，设置账号密码
宝塔中网站设置里面在网站目录中打开密码访问，设置账号密码即可。
