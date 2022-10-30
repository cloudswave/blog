[开始使用Github Issues写博客](https://github.com/cloudswave/blog/issues/4)

## 背景
- 简书频繁下架文章，每天只能发布两篇
- CSDN页面太杂，写作不够专注，客户端不好用
- Github Web版和App都非常简洁实用，可以随意写文章不需要担心审核问题
- 可以通过Actions工作流来同步文章到Github Pages和其他博客平台
- 学习Github Action开发，学习机遇Github到自动化构建部署流程

<!--增加下面这个more注释为hexo提取本文摘要防止hexo博客主页显示全文-->
<!--more-->
## 需求和实现
### 自动生成README目录
仿照[.github/workflows/issue2readme.yaml](https://github.com/cloudswave/blog/blob/master/.github/workflows/issue2readme.yaml)在仓库里增加workflows配置文件进行修改，[说明文档](https://github.com/bxb100/issueblog#readme)，最后生成[效果](https://github.com/cloudswave/blog/#readme)

### 自动构建Hexo发布到Github Pages
仿照[.github/workflows/issue2hexo.yaml](https://github.com/cloudswave/blog/blob/master/.github/workflows/issue2hexo.yaml)在仓库里增加workflows配置文件，根据自身情况修改环境变量，最后发布的效果可查看[我的Hexo博客](http://cloudswave.github.io)
```
env:
  # github pages repo
  PUBLISH_REPOSITORY: cloudswave/cloudswave.github.io
  # GITHUB TOKEN 可以试一试将secrets.BLOG_TOKEN可以直接改成github内置的密钥secrets.BLOG_TOKEN，但是我的会报如下图的权限错误
  GITHUB_TOKEN: ${{ secrets.BLOG_TOKEN }}
```
![image](https://user-images.githubusercontent.com/5915548/198711776-db2ff7fc-f7ec-4eec-b67f-8a1d162a1964.png)

BLOG_TOKEN生成指导：
1. [creating-a-personal-access-token-classic](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token#creating-a-personal-access-token-classic)不知道选那些权限选择的时候全部选上吧
![image](https://user-images.githubusercontent.com/5915548/198711260-eb252739-105f-472b-8894-a016fc1d28ad.png)

3. [creating-encrypted-secrets-for-a-repository](https://docs.github.com/cn/actions/security-guides/encrypted-secrets#creating-encrypted-secrets-for-a-repository)

![image](https://user-images.githubusercontent.com/5915548/198710315-f180cdb0-baa8-4c16-aa92-fed599bd354f.png)
**另外还需要Pages仓库的权限配置**，如下图：
![image](https://user-images.githubusercontent.com/5915548/198712542-b1c8edfc-4170-426b-885c-0ba691a6934b.png)
![image](https://user-images.githubusercontent.com/5915548/198712367-39502701-4d41-4468-97b7-9b808b92ae62.png)

### 自动发布文章到其他博客平台用来引流
- 发布到CSDN, 参考[issue2csdn](https://github.com/cloudswave/blog/blob/master/.github/workflows/issue2csdn.yml)增加workflows配置文件，[说明文档](https://github.com/cloudswave/issue2csdn#readme)

### 提示
如果你只想在issue添加Miestone为publish的时候才发布更新，可以修改yml里的触发器，如：
```
on:
  issues:
    # Sufficient to trigger this workflow when an issue is milestoned
    types: [milestoned]
    # types: [unlabeled, labeled, edited, milestoned]
```

可以在Actions页面查看workflow执行日志和禁止某个workflow执行
<img width="805" alt="action-guide" src="https://user-images.githubusercontent.com/5915548/198864861-03313485-2a81-4f76-ba44-ec24254a619a.png">

## 参考文档
- https://github.com/Solybum/hexo-deploy
- https://github.com/zenghongtu/github-issue-to-hexo
- https://github.com/onblog/BlogHelper
- https://github.com/JoeyBling/yilia-plus-demo