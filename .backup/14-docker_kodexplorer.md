[docker kodexplorer](https://github.com/cloudswave/blog/issues/14)

```
 docker run -d \
--name kode \
-e PUID=$UID \
-e PGID=$GID \
-p 80:80 \
-v /root/kode:/var/www/html \
-v /root:/father \
kodcloud/kodexplorer
```