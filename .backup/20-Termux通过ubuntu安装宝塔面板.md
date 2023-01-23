[Termux通过ubuntu安装宝塔面板](https://github.com/cloudswave/blog/issues/20)

# Termux下Atilo安装ubuntu
参考：https://www.jianshu.com/p/a9b30ea2ae66
# arm64快速安装宝塔
参考：https://gitee.com/pdusb/pdusb-fast-btpanel
ubuntu下：
```
git clone https://gitee.com/pdusb/pdusb-fast-btpanel.git
cd pdusb-fast-btpanel
./pdbolt-inst-bt-acel.sh
bash /tmp/btp/pdbolt-bt-install/install.sh

/etc/init.d/bt restart
# 进入宝塔工具面板，可查看修改端口和密码
bt
```
# 错误处理记录
- ERROR: cannot verify download.bt.cn's certificate, issued by ‘CN=sslTrus (RSA) OV CA,O=sslTrus,C=CN’: Unable to locally verify the issuer's authority. To connect to download.bt.cn insecurely, use `--no-check-certificate' ERROR: Download python env fielded.
关闭证书验证：echo "check_certificate = off" >> ~/.wgetrc

-  报错chmod: cannot access '/tmp/btp/pyenv/bin/python3.7': Operation not permitted...
```
...
root@localhost:~/pdusb-fast-btpanel# chmod 777 /tmp/btp/pyenv/bin/ -R
chmod: cannot access '/tmp/btp/pyenv/bin/python3.7': Operation not permitted
chmod: cannot access '/tmp/btp/pyenv/bin/.l2s.python3.70001': Operation not permitted
chmod: cannot access '/tmp/btp/pyenv/bin/python3.7m': Operation not permitted
...
```
解压pyenv-ubuntu2004-3.7.8-aarch64.tar.xz出错导致，重新解压后替换出错文件

- 宝塔启动失败
```
...
Traceback (most recent call last):
  File "/www/server/panel/BT-Panel", line 27, in <module>
    from BTPanel import app,sys,public
  File "/www/server/panel/BTPanel/__init__.py", line 11, in <module>
    import json
ModuleNotFoundError: No module named 'json'
...
```
权限修复：chmod 777 -R /www