[Android使用游猴脚本](https://github.com/cloudswave/blog/issues/45)

经过测试，Via浏览器和游猴手机版可安装一些脚本，但不支持一些特殊API，比如GM_cookie和GM_xmlhttpRequest，故尝试寻找开源项目，通过github找到一些项目
- [ChromeXt](https://github.com/JingMatrix/ChromeXt)，需配合[LSPatch](https://github.com/JingMatrix/LSPatch)和浏览器使用，推荐
- [Android-WebMonkey](https://github.com/warren-bank/Android-WebMonkey)，目前不支持GM_cookie

ChromeXt使用步骤:
1. 下载 lspatch-release [GitHub Actions](https://github.com/JingMatrix/LSPatch/actions)，解压安装manager-v0.5.1-397-release.apk；
2. 下载安装 ChromeXt.apk  [releases](https://github.com/JingMatrix/ChromeXt/releases)；
3.  手机上:通过LSPatch(需要使用shizuku服务配对adb)选择某个浏览器.apk，关联上ChromeXt模块，生成新的安装包并安装，或者电脑上使用`java -jar lspatch.jar 浏览器.apk -d -v -m ChromeXt-signed.apk --force`命令生成新的浏览器安装包；
4. 通过浏览器访问脚本市场测试安装任意一款脚本，正常会发现有ChromeXt提示安装弹窗；
5. 通过[onrender.com](https://jianyu-ma.onrender.com/ChromeXt/), [netlify.app](https://jianyu-ma.netlify.app/ChromeXt/)管理脚本，如果要修改脚本内容需要在步骤4进行，或者浏览器打开手机本地脚本；
