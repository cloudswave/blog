[用浏览器远程控制 Android 手机，搭建云手机](https://github.com/cloudswave/blog/issues/34)

项目地址： [GitHub – NetrisTV/ws-scrcpy: Web client prototype for scrcpy.](https://github.com/NetrisTV/ws-scrcpy)
### docker版
```
# docker run --name ws-scrcpy -d -p 8000:8000 scavin/ws-scrcpy
docker run --name ws-scrcpy -d --net=host  scavin/ws-scrcpy   # 使用--net=host 保证能访问到安卓设备
docker exec ws-scrcpy adb connect 安卓IP地址:5555 # 如果使用zerotier实现内网穿透，则在zerotier后台查看分配到ip
```
访问：http://服务器ip:8000
<img width="945" alt="image" src="https://github.com/cloudswave/blog/assets/5915548/261e036f-4e9d-4878-8ea8-bf603866cf64">


### Andorid手机打开网络调试
- 电脑安装 adb工具套件[SDK Platform Tools](https://developer.android.com/studio/releases/platform-tools)
- 打开开发者模式
- 使用 USB 连接 Android 设备，并授权
- 终端输入 adb tcpip 5555

### 安装zerotier实现内网穿透
- 安卓手机直接下载zerotier app：https://www.zerotier.com/download/
- 服务器端docker版
```
官方镜像：https://registry.hub.docker.com/r/bltavares/zerotier
拉取镜像
docker pull bltavares/zerotier
配置镜像
docker run --device=/dev/net/tun \
--name zerotier-one \
--net=host \
--restart=always \
--cap-add=NET_ADMIN \
--cap-add=SYS_ADMIN \
-v /var/lib/zerotier-one:/var/lib/zerotier-one \
bltavares/zerotier:latest
加入zerotier网络
docker exec zerotier-one zerotier-cli join [网络ID]
显示 200 join ok 即添加成功
```
debian版：
```
添加Zerotier存储库
curl -s https://install.zerotier.com | sudo bash
安装Zerotier
sudo apt-get install zerotier-one
启动Zerotier
sudo systemctl start zerotier-one
查看Zerotier状态
sudo zerotier-cli status
加入网络
zerotier-cli join [网络ID]
```
也可以使用frp或者nps来实现内网穿透达到对安卓的控制
### 参考
- https://www.appinn.com/ws-scrcpy
- https://github.com/Genymobile/scrcpy
<!--csdn-article-id:132594906-->
