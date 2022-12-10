[挂载alist webdav](https://github.com/cloudswave/blog/issues/10)

mount -t davfs http://141.147.162.129:5244/dav  ~/alist

---

<a id="issuecomment-1338753484"></a>
使用cadaver操作webdav
```
cadaver http://141.147.162.129:5244/dav

dav:/dav/> ls
Listing collection `/dav/': succeeded.
dav:/dav/> cd tianyi
dav:/dav/tianyi/> put /root/download/zh-cn_windows_11_business_editions_version_22h2_updated_nov_2022_x64_dvd_3623b3e1.iso
Uploading /root/download/zh-cn_windows_11_business_editions_version_22h2_updated_nov_2022_x64_dvd_3623b3e1.iso to `/dav/tianyi/zh-cn_windows_11_business_editions_version_22h2_updated_nov_2022_x64_dvd_3623b3e1.iso':
Progress: [=============================>] 100.0% of 5567041536 bytes succeeded.

```

---

<a id="issuecomment-1340346819"></a>
开机自启配置：https://zhuanlan.zhihu.com/p/265870275

---

<a id="issuecomment-1344520587"></a>
```
cadaver https://files.server.com/webdav/ <<EOF
cd /backups/$APPNAME
delete $DBNAME$TODAYSDATE-db.aes
delete $APPNAME$LOGDATE-log.aes
put $BACKUPPATH/$DBNAME$TODAYSDATE-db.aes
put $BACKUPPATH/$APPNAME$LOGDATE-log.aes
quit
EOF
```

---

<a id="issuecomment-1345158833"></a>
webdav上传大文件建议使用rclone工具，davfs容易失败
rclone move -v /root/download alist:/teambition -P
参考：
https://blog.csdn.net/w670165403/article/details/87887300
https://p3terx.com/archives/offline-download-of-onedrive-gdrive.html