[安卓5下Termux通过ubuntu安装alist](https://github.com/cloudswave/blog/issues/19)

安卓7以上可以直接通过pkg install alist来安装alist，安卓5安装ubuntu需要64位termux，通过Atilo套娃其他linux来安装alist
## 安装
termux下：
```
apt install tmux
echo "deb [trusted=yes arch=all] https://yadominjinta.github.io/files/ termux extras" >> $PREFIX/etc/apt/sources.list.d/atilo.list
apt update && apt install atilo-cn
atilo images #可以安装的镜像列表
atilo pull ubuntu
ls ~/.atilo #可以备份该路径以便日后有问题恢复系统
atilo run ubuntu #启动进入ubuntu
```
ubuntu下：
```
apt install curl
apt install tmux
curl -fsSL "https://alist.nn.ci/v3.sh" | bash -s install
/opt/alist/alist admin #查看密码
/opt/alist/alist server
```
Atilo介绍：
```
Atilo 是一个用来帮助你在termux上安装不同的GNU/Linux发行版的程序
命令:
images 列出可用镜像
remove 移除本地的镜像
pull 拉取远程的镜像
run 运行镜像
clean 清除缓存
help 帮助

$ atilo images
+----------+------------+--------+--------+
| 名称 | 版本 | 已安装 | 可安装 |
+----------+------------+--------+--------+
| alpine | 3.12 | False | True |
| centos | 8 | False | True |
| debian | buster | False | True |
| fedora | 33 | False | True |
| kali | latest | False | True |
| opensuse | Tumbleweed | False | True |
| ubuntu | focal | False | True |
+----------+------------+--------+--------+
```

## 自启配置
termux下：
```
echo "~/startup.sh" >> ~/.bashrc
echo "~/startup.sh" >> ~/.atilo/ubuntu/root/.bashrc
```
vi ~/startup.sh增加：
```
if pgrep -f "ubuntu" >/dev/null
  then
    echo "ubuntu运行中..."
  else
  tmux new -d -s ubuntu 'atilo run ubuntu'
  echo "ubuntu已开启..."
fi
```
vi ~/.atilo/ubuntu/root/startup.sh：
```
if pgrep -x "alist" >/dev/null
  then
    echo "alist运行中..."
  else
  tmux new -d -s alist '/opt/alist/alist server'
  echo "alist已开启..."
fi
```
<!--csdn-article-id:128754459-->