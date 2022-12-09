[davfs挂载webdav](https://github.com/cloudswave/blog/issues/10)

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