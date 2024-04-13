[docker安装安卓](https://github.com/cloudswave/blog/issues/35)

## 项目:redroid
地址：https://github.com/remote-android/redroid-doc
```
docker run -itd --privileged --memory-swappiness=0 --pull always -v /opt/data12.1:/data -p 5555:5555 --name redroid12-1 redroid/redroid:12.0.0-latest androidboot.redroid_width=1080 androidboot.redroid_height=1920 androidboot.redroid_dpi=480 androidboot.redroid_gpu_mode=guest ro.secure=0
```
arm服务器验证可行，配合ws-scrcpy实现浏览器远程控制

## 项目:docker-android
> 个人未搭建成功，请自行测试验证

官方github地址：[https://github.com/budtmo/docker-android](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fbudtmo%2Fdocker-android)
cpu必须支持虚拟化，检查cpu是否支持虚拟化
```
# 查询是否支持虚拟化
egrep "(svm|vmx)" /proc/cpuinfo >/dev/null && echo '支持' || echo '不支持'

# 安装
docker run -d -p 6080:6080 -e EMULATOR_DEVICE="Samsung Galaxy S10" -e WEB_VNC=true --device /dev/kvm --name android-container budtmo/docker-android:emulator_11.0

# 访问
http://docker-host-ip-address:6080
```

手机型号号可以通过更改device参数来修改
Type | Device Name
-- | --
Phone | Samsung Galaxy S10
Phone | Samsung Galaxy S9
Phone | Samsung Galaxy S8
Phone | Samsung Galaxy S7 Edge
Phone | Samsung Galaxy S7
Phone | Samsung Galaxy S6
Phone | Nexus 4
Phone | Nexus 5
Phone | Nexus One
Phone | Nexus S
Tablet | Nexus 7

</div><br/>
