[docker版alist和aria2配合使用](https://github.com/cloudswave/blog/issues/12)


```
docker run -d --restart=always -v /etc/alist:/opt/alist/data -p 5244:5244 --name="alist" xhofe/alist:latest

docker run -d \
--name aria2 \
--restart unless-stopped \
--log-opt max-size=1m \
-e PUID=$UID \
-e PGID=$GID \
-e UMASK_SET=022 \
-e RPC_SECRET=pac_password \
-e RPC_PORT=6800 \
-e LISTEN_PORT=6888 \
-p 16800:6800 \
-p 16888:6888 \
-p 16888:6888/udp \
-v /root/aria2/config:/config \
-v /etc/alist/temp/downloads:/downloads \
-v /etc/alist/temp/aria2:/opt/alist/data/temp/aria2 \
p3terx/aria2-pro
```
-v /etc/alist/temp/aria2:/opt/alist/data/temp/aria2 注意这个地方是为了让离线下载路径可以共享
<!--csdn-article-id:128254475-->