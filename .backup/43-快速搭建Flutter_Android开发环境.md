[快速搭建Flutter Android开发环境](https://github.com/cloudswave/blog/issues/43)

# 利用github codespaces容器
如：https://github.com/cloudswave/flutter_browser_app/blob/master/.devcontainer/devcontainer.json
版本查询：https://github.com/sambyeol/flutter-devcontainer/pkgs/container/flutter-devcontainer
项目目录下创建.devcontainer/devcontainer.json
```
{
    "image": "ghcr.io/sambyeol/flutter-devcontainer:<flutter-version>",
    "runArgs": [ "--init" ],
    "extensions": [
        "dart-code.dart-code",
        "dart-code.flutter"
    ],
}
```
参考：https://github.com/sambyeol/flutter-devcontainer
# 利用脚本
```
#!/bin/bash
# 设置变量
export PROJECT_PATH=`pwd`;
export ANDROID_HOME=${PROJECT_PATH}/env/android
export FLUTTER_HOME=${PROJECT_PATH}/env/flutter
export JAVA_HOME=${PROJECT_PATH}/env/jdk-11
export PUB_HOSTED_URL=https://pub.flutter-io.cn
export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn

# 环境变量写入到~/.bashrc
echo "export ANDROID_HOME=$ANDROID_HOME" >>  ~/.bashrc
echo "export FLUTTER_HOME=$FLUTTER_HOME" >>  ~/.bashrc
echo "export JAVA_HOME=$JAVA_HOME" >>  ~/.bashrc
echo "export PUB_HOSTED_URL=https://pub.flutter-io.cn" >>  ~/.bashrc
echo "export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn" >>  ~/.bashrc
echo 'export PATH=$ANDROID_HOME/cmdline-tools/latest/bin:$ANDROID_HOME/platform-tools/:${FLUTTER_HOME}/bin:${JAVA_HOME}/bin:$PATH' >> ~/.bashrc
source  ~/.bashrc

 mkdir $PROJECT_PATH/env

# JAVA
if test -d $JAVA_HOME; then
    echo "$JAVA_HOME目录存在"
else
    echo "安装JDK11环境"
    cd ${PROJECT_PATH}/env
    curl -o openjdk-11_linux-x64_bin.tar.gz https://download.java.net/java/ga/jdk11/openjdk-11_linux-x64_bin.tar.gz
    tar xzvf openjdk-11_linux-x64_bin.tar.gz
    rm -rf openjdk-11_linux-x64_bin.tar.gz
fi

# Android
if test -d $ANDROID_HOME; then
    echo "$ANDROID_HOME目录存在"
else
    echo "安装Android环境"
    cd ${PROJECT_PATH}/env
    mkdir $ANDROID_HOME
    mkdir -p ${ANDROID_HOME}/cmdline-tools/
    cd $ANDROID_HOME/cmdline-tools/
    SDK_TOOL_URL="https://dl.google.com/android/repository/commandlinetools-linux-7583922_latest.zip";
    # https://dl.google.com/android/repository/commandlinetools-linux-7583922_latest.zip
    curl -o sdk.zip $SDK_TOOL_URL
    unzip sdk.zip
    rm -rf sdk.zip
    mv cmdline-tools latest
    echo y | $ANDROID_HOME/cmdline-tools/latest/bin/sdkmanager "build-tools;30.0.3"
    echo y | $ANDROID_HOME/cmdline-tools/latest/bin/sdkmanager "platforms;android-33"
    echo y | $ANDROID_HOME/cmdline-tools/latest/bin/sdkmanager "platform-tools"
fi


# Flutter
if test -d $FLUTTER_HOME; then
    echo "$FLUTTER_HOME目录存在"
else
    echo "安装Flutter环境"
    cd ${PROJECT_PATH}/env
    curl -o flutter_linux_3.13.9-stable.tar.xz https://storage.googleapis.com/flutter_infra_release/releases/stable/linux/flutter_linux_3.13.9-stable.tar.xz
    tar xf ./flutter_linux_3.13.9-stable.tar.xz
    rm -rf flutter_linux_3.13.9-stable.tar.xz
    git config --global --add safe.directory $FLUTTER_HOME
    $FLUTTER_HOME/bin/flutter channel
    yes y |$FLUTTER_HOME/bin/flutter doctor --android-licenses
fi
git config --global --add safe.directory $FLUTTER_HOME

# gradle
if test -d ${PROJECT_PATH}/env/.gradle; then
    echo "${PROJECT_PATH}/env/.gradle目录存在"
else
    echo "配置Gradle环境，下载离线包gradle-7.5-all.zip"
    mkdir ${PROJECT_PATH}/env/.gradle
    cp -n ${PROJECT_PATH}/init.gradle ${PROJECT_PATH}/env/.gradle/
    mkdir ${PROJECT_PATH}/env/.gradle/wrapper/dists
    cd ${PROJECT_PATH}/env/.gradle/wrapper/dists
    wget https://github.moeyy.xyz/https://github.com/gradle/gradle-distributions/releases/download/v7.5.0/gradle-7.5-all.zip # https://services.gradle.org/distributions/
fi


# 缓存目录迁移
ln -s ${PROJECT_PATH}/env/.gradle ~/
mkdir ${PROJECT_PATH}/env/.pub-cache
ln -s ${PROJECT_PATH}/env/.pub-cache ~/

cd $PROJECT_PATH
mkdir workspace
echo "
1. 如果环境搭建成功，则不要删除env环境目录，否则可以删除env下对应环境重新搭建环境
2. 如果环境变量丢失可以执行source init.sh
3. 你可以将flutter项目代码存放在workspace目录中
"
```
