---
title: 开始使用Github Issues写博客
date: 10/29/2022, 3:05:23 AM
tags: Top
categories: Top
---

## 背景
- 简书频繁下架文章，每天只能发布两篇
- CSDN写过不够专注，客户端不好用
- Github Web版和App都非常简洁实用，可以随意写文章不需要担心审核问题
- 可以通过Actions工作流来同步文章到Github Pages和其他博客平台

<!--more-->
## 需求和实现
### 自动生成README目录
仓库里增加workflows配置文件仿照[.github/workflows/issue2readme.yaml](https://github.com/cloudswave/blog/blob/master/.github/workflows/issue2readme.yaml)进行修改，[说明文档](https://github.com/bxb100/issueblog#readme)

### 自动构建Hexo发布到Github Pages
仓库里增加workflows配置文件放照[.github/workflows/issue2hexo.yaml](https://github.com/cloudswave/blog/blob/master/.github/workflows/issue2hexo.yaml)，根据自身情况修改环境变量
```
env:
  # hexo源文件仓库
  BLOG_SOURCE: cloudswave/blog
  # issue链接
  ISSUE_URL: https://github.com/cloudswave/blog/issues
  # blog部署的页面
  BLOG: cloudswave/blog
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
todo
