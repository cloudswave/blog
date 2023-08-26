[alist编译记录](https://github.com/cloudswave/blog/issues/29)

## 服务器环境
Linux version 3.10.0-327.el7.x86_64 (builder@kbuilder.dev.centos.org) (gcc version 4.8.3 20140911 (Red Hat 4.8.3-9) (GCC) ) #1 SMP Thu Nov 19 22:10:57 UTC 2015
## 前端
```
yum install npm
npm install -g npm@9.5.0
npm install pnpm -g
git clone --recursive https://github.com/alist-org/alist-web.git
cd alist-web
pnpm run build
```
## 后端
```
yum install screen
screen -R alist # 开screen避免掉线编译中断
rpm --import https://mirror.go-repo.io/centos/RPM-GPG-KEY-GO-REPO
curl -s https://mirror.go-repo.io/centos/go-repo.repo | tee /etc/yum.repos.d/go-repo.repo
yum install golang
git clone https://github.com/alist-org/alist.git
cd alist
vi build-local.sh # 新建一个本地编译的脚本，复制以下内容
------
rm -rf ./public/dist/*
cp -r ../alist-web/dist ./public/ # 注意前端代码路径
appName="alist"
builtAt="$(date +'%F %T %z')"
goVersion=$(go version | sed 's/go version //')
gitAuthor=$(git show -s --format='format:%aN <%ae>' HEAD)
gitCommit=$(git log --pretty=format:"%h" -1)
version=$(git describe --long --tags --dirty --always)
webVersion=$(wget -qO- -t1 -T2 "https://api.github.com/repos/alist-org/alist-web/releases/latest" | grep "tag_name" | head -n 1 | awk -F ":" '{print $2}' | sed 's/\"//g;s/,//g;s/ //g')
ldflags="\
-w -s \
-X 'github.com/alist-org/alist/v3/internal/conf.BuiltAt=$builtAt' \
-X 'github.com/alist-org/alist/v3/internal/conf.GoVersion=$goVersion' \
-X 'github.com/alist-org/alist/v3/internal/conf.GitAuthor=$gitAuthor' \
-X 'github.com/alist-org/alist/v3/internal/conf.GitCommit=$gitCommit' \
-X 'github.com/alist-org/alist/v3/internal/conf.Version=$version' \
-X 'github.com/alist-org/alist/v3/internal/conf.WebVersion=$webVersion' \
"
go build -ldflags="$ldflags" 
-----

bash build-local.sh  # 编译

./alist admin # 查看管理员信息
./alist server # 启动服务
vi ./data/config.json # 可修改端口
```
## 国际化
```
方案1：
# crowdin 环境变量配置，先去crowdin官网注册获取api-token，创建一个project获取project id
# https://crowdin.com/project/alist/zh-CN
export CROWDIN_PROJECT_ID=***
export CROWDIN_PERSONAL_TOKEN=***
pnpm run i18n:release

方案2：直接下载中文翻译文件
[root@ecs-G3x6u alist]# wget https://crowdin.com/backend/download/project/alist/zh-CN.zip --no-check-certificate
[root@ecs-G3x6u alist]# unzip zh-CN.zip 
Archive:  zh-CN.zip
   creating: src/
   creating: src/lang/
   creating: src/lang/zh-CN/
  inflating: src/lang/zh-CN/settings.json  
  inflating: src/lang/zh-CN/metas.json  
  inflating: src/lang/zh-CN/settings_other.json  
  inflating: src/lang/zh-CN/manage.json  
  inflating: src/lang/zh-CN/storages.json  
  inflating: src/lang/zh-CN/login.json  
  inflating: src/lang/zh-CN/drivers.json  
  inflating: src/lang/zh-CN/global.json  
  inflating: src/lang/zh-CN/users.json  
  inflating: src/lang/zh-CN/home.json  
 extracting: src/lang/zh-CN/index.json  
  inflating: src/lang/zh-CN/tasks.json  
  inflating: src/lang/zh-CN/br.json  
  inflating: src/lang/zh-CN/indexes.json  
[root@ecs-G3x6u alist]# ls
alist  alist-web  src  zh-CN.zip
[root@ecs-G3x6u alist]# cp -r ./src/lang/zh-CN ./alist-web/src/
app/        components/ hooks/      index.d.ts  lang/       main.tsx    pages/      store/      types/      utils/      
[root@ecs-G3x6u alist]# cp -r ./src/lang/zh-CN ./alist-web/src/lang/
[root@ecs-G3x6u alist]# cd alist-web/
[root@ecs-G3x6u alist-web]# node ./scripts/i18n.mjs
```
## 参考
- https://alist.nn.ci/zh/guide/install/source.html
- https://blog.csdn.net/qq_25919879/article/details/128400309

<!--csdn-article-id:131495456-->
