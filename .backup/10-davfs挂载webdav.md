[davfs挂载webdav](https://github.com/cloudswave/blog/issues/10)

mount -t davfs http://141.147.162.129:5244/dav  ~/alist

---

<a id="issuecomment-1338753484"></a>
使用cadaver操作webdav
```
cadaver http://141.147.162.129:5244/dav

dav:/dav/> ls
Listing collection `/dav/': succeeded.
Coll:   三丰云                              0  Oct 26 16:35
Coll:   TeraCloud                              0  Dec  5 16:03
Coll:   TeraCloud2                             0  Dec  5 16:12
Coll:   ali                                    0  Dec  6 01:16
Coll:   arm                                    0  Nov  2 13:12
Coll:   lanzou                                 0  Dec  6 01:16
Coll:   local                                  0  Oct 29 13:26
Coll:   mi2                                    0  Dec  4 13:07
Coll:   pikpak1                                0  Dec  5 16:28
Coll:   pikpak2                                0  Dec  5 16:32
Coll:   quark                                  0  Dec  6 01:17
Coll:   tianyi                                 0  Dec  6 01:18 
dav:/dav/> cd tianyi
dav:/dav/tianyi/> put /root/download/zh-cn_windows_11_business_editions_version_22h2_updated_nov_2022_x64_dvd_3623b3e1.iso
Uploading /root/download/zh-cn_windows_11_business_editions_version_22h2_updated_nov_2022_x64_dvd_3623b3e1.iso to `/dav/tianyi/zh-cn_windows_11_business_editions_version_22h2_updated_nov_2022_x64_dvd_3623b3e1.iso':
Progress: [=============================>] 100.0% of 5567041536 bytes succeeded.

```