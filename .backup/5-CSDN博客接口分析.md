[CSDN博客接口分析](https://github.com/cloudswave/blog/issues/5)

```javascript
var config = require("./mcConfig");
var request = require('request');
var querystring = require('querystring');

function createPost(oPost) {

  var url = "https://mp.csdn.net/mdeditor/saveArticle";
  var oBody = {
          title: oPost.title,
          markdowncontent: oPost.body,
          tags:"Fiori",
          categories:"Fiori",
          channel:"14",
          type:"original",
          articleedittype:"1",
          content: oPost.body
        };

var formData = querystring.stringify(oBody);
var contentLength = formData.length;

var createPostOptions = {
        url: url,
        method: "POST",  
        headers: {
            "content-type": "application/x-www-form-urlencoded",
            "Content-Length": contentLength,
            "origin" :"https://mp.csdn.net",
            "referer" :"https://mp.csdn.net/mdeditor",
            "User-Agent" :"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/74.0.3729.131 Safari/537.36",
            "cookie": config.cookie
        },
        body: formData
};

  return new Promise(function(resolve,reject){
      var requestC = request.defaults({jar: true});
      console.log("Step1: create post via url: "   url );

      requestC(createPostOptions,function(error,response,body){
       
       if(error){
          reject(error);
       }
       console.log("response: "   body);
       resolve(body);
      }); 
     });
}

module.exports = createPost;

var request = require('request');

function getIssue(issueNumber) {

  var url = "https://api.github.com/repos/i042416/KnowlegeRepository/issues/"   issueNumber;

  var getIssueOptions = {
        url: url,
        method: "GET",
        json:true,
        headers: {
            "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/74.0.3729.131 Safari/537.36"
        }
  };

  return new Promise(function(resolve,reject){
      var requestC = request.defaults({jar: true});
      console.log("Step1: get issue detail via url: "   url );

      requestC(getIssueOptions,function(error,response,body){
        if(error){
          console.log("error occurred: "   error);
          reject(error);
        }
        console.log("title:"   body.title);
        console.log("body: "   body.body);
        for( var i = 0; i < body.labels.length; i  ){
          console.log("label: "   body.labels[i].name);
        }
        resolve(body);
      }); 
     });
}

module.exports = getIssue;

var readIssue = require("./readIssueMod");
var createPost = require("./createPostMod");

readIssue(2215).then(createPost).catch((error)=>{console.log("error: "   error)});
```
https://www.jianshu.com/p/ae4fe3c6ccf7

---

<a id="issuecomment-1295841444"></a>
```javascript
const https = require('https');
const fetch = require('node-fetch');
const DataStore = require('../app-store');
const dataStore = new DataStore();
const FormData = require('form-data');
const fs = require('fs');

const extMap = {
  'jpg': 'jpeg',
  'jpeg': 'jpeg',
  'png': 'png',
  'gif': 'gif',
}

//上传图片到CSDN
function uploadPictureToCSDN(filePath) {
    return new Promise((resolve, reject) => {
        const ext = extMap[filePath.split('.').pop().toLowerCase()] || 'png';

        fetch("https://imgservice.csdn.net/direct/v1.0/image/upload?watermark=&type=blog&rtype=markdown", {
          "headers": {
            "content-type": "application/json",
            "x-image-app": "direct_blog",
            "x-image-dir": "direct",
            "x-image-suffix": ext,
            "cookie": dataStore.getCSDNCookies(),
            "Referer": "https://editor.csdn.net/",
          }
        }).then(result => result.json())
          .then(result => {
            console.log('get access info:', result);
            if (result.code === 200) {
              const accessId = result.data.accessId;
              const callbackUrl = result.data.callbackUrl;
              const remoteFilePath = result.data.filePath;
              const url = result.data.host;
              const policy = result.data.policy;
              const signature = result.data.signature;

              let formData = new FormData();
              formData.append('key', remoteFilePath);
              formData.append('OSSAccessKeyId', accessId);
              formData.append('policy', policy);
              formData.append('signature', signature);
              formData.append('success_action_status', '200');
              formData.append('callback', callbackUrl);
              formData.append('file', fs.createReadStream(filePath));

              let headers = formData.getHeaders();
              headers.Cookie = dataStore.getCSDNCookies();
              headers["user-agent"] = "Mozilla/5.0";
              headers.Referer = "https://editor.csdn.net/";
              headers.ContentType = 'multipart/form-data';
              headers.Accept = 'application/json';

              // post formData to host
              fetch(url, {
                method: 'POST',
                body: formData,
                headers: headers
              }).then(result => result.json())
                .then(result => {
                  if (result.code === 200) {
                    resolve(result.data.imageUrl)
                  } else {
                    reject('上传图片失败,' + result.msg)
                  }
                })
                .catch(error => {
                  reject('上传图片失败,' + error)
                })
            } else {
              reject('上传图片失败,' + result.msg)
            }
          })
          .catch(error => {
            reject('上传图片失败,' + error)
          })
    })
}

//上传文章到CSDN
function publishArticleToCSDN(title, markdowncontent, content, isPublish) {
    return new Promise((resolve, reject) => {
        const parms = {
            title: title,
            markdowncontent: markdowncontent,
            content: content,
            readType: "public",
            not_auto_saved: "1",
            source: "pc_mdeditor",
            level: 1
        };
        if (isPublish) {
            parms['status'] = 0;
            parms['type'] = 'original';
            parms['Description'] = content.toString().substring(0,100);
            parms['authorized_status'] = false;
            parms['categories'] = '';
            parms['original_link'] = '';
            parms['resource_url'] = '';
            parms['tags'] = '经验分享'
        }else {
            parms['status'] = 2
        }
        const json = JSON.stringify(parms);
        let request = https.request({
                                        host: 'blog-console-api.csdn.net',
                                        method: 'POST',
                                        path: '/v1/mdeditor/saveArticle',
                                        headers: {
                                            "content-type": "application/json",
                                            "cookie": dataStore.getCSDNCookies(),
                                            "user-agent": "Mozilla/5.0"
                                        }
                                    }, function (res) {
            let str = '';
            res.on('data', function (buffer) {
                       str += buffer;
                   }
            );
            res.on('end', () => {
                const result = JSON.parse(str);
                if (res.statusCode === 200) {
                    if (result.code === 200) {
                        const url = isPublish ? result.data.url
                                              : 'https://editor.csdn.net/md/?articleId='
                                                + result.data.id;
                        resolve(url)
                    } else {
                        reject('发布失败,' + result.msg)
                    }
                } else {
                    reject('发布失败: ' + res.statusCode + '\n'+decodeURI(result.msg))
                }
            });
        });

        request.write(json);
        request.end();

        request.on('error', function (e) {
            reject('网络连接异常'+e.message)
        });
    })
}

exports.uploadPictureToCSDN = uploadPictureToCSDN;
exports.publishArticleToCSDN = publishArticleToCSDN;
```
https://github.com/onblogs/BlogHelper/blob/master/src/blog/csdn.js

---

<a id="issuecomment-1296133554"></a>
更新博客时浏览器抓包生成的Node.jsfetch，所以只需要发布博客的时候添加文章id可以更新某个文章：
```
fetch("https://bizapi.csdn.net/blog-console-api/v3/mdeditor/saveArticle", {
  "headers": {
    "accept": "*/*",
    "accept-language": "zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6",
    "content-type": "application/json",
    "sec-ch-ua": "\".Not/A)Brand\";v=\"99\", \"Microsoft Edge\";v=\"103\", \"Chromium\";v=\"103\"",
    "sec-ch-ua-mobile": "?0",
    "sec-ch-ua-platform": "\"macOS\"",
    "sec-fetch-dest": "empty",
    "sec-fetch-mode": "cors",
    "sec-fetch-site": "same-site",
    "x-ca-key": "203803574",
    "x-ca-nonce": "09892f4a-53a1-41d6-8e0b-653080a101fe",
    "x-ca-signature": "QbBoF0NaJoJ8ygqYfxPV/j/Cvh8Ko1Ui2xOPssjM+Zs=",
    "x-ca-signature-headers": "x-ca-key,x-ca-nonce",
    "cookie": "***********",
    "Referer": "https://editor.csdn.net/",
    "Referrer-Policy": "strict-origin-when-cross-origin"
  },
  "body": "{\"id\":\"127599075\",\"title\":\"开始使用Github Issues写博客\",\"markdowncontent\":\"## 背景\\n- 简书频繁下架文章，每天只能发布两篇\\n- CSDN页面太杂，写作不够专注，客户端不好用\\n- Github Web版和App都非常简洁实用，可以随意写文章不需要担心审核问题\\n- 可以通过Actions工作流来同步文章到Github Pages和其他博客平台\\n- 学习Github Action开发，学习机遇Github到自动化构建部署流程\\n\\n<!--增加下面这个more注释为hexo提取本文摘要防止hexo博客主页显示全文-->\\n<!--more-->\\n## 需求和实现\\n### 自动生成README目录\\n仿照[.github/workflows/issue2readme.yaml](https://github.com/cloudswave/blog/blob/master/.github/workflows/issue2readme.yaml)在仓库里增加workflows配置文件进行修改，[说明文档](https://github.com/bxb100/issueblog#readme)，最后生成[效果](https://github.com/cloudswave/blog/#readme)\\n\\n### 自动构建Hexo发布到Github Pages\\n仿照[.github/workflows/issue2hexo.yaml](https://github.com/cloudswave/blog/blob/master/.github/workflows/issue2hexo.yaml)在仓库里增加workflows配置文件，根据自身情况修改环境变量，最后发布的效果可查看[我的Hexo博客](cloudswave.github.io)\\n```\\nenv:\\n  # github pages repo\\n  PUBLISH_REPOSITORY: cloudswave/cloudswave.github.io\\n  # GITHUB TOKEN 可以试一试将secrets.BLOG_TOKEN可以直接改成github内置的密钥secrets.BLOG_TOKEN，但是我的会报如下图的权限错误\\n  GITHUB_TOKEN: ${{ secrets.BLOG_TOKEN }}\\n```\\n![image](https://img-blog.csdnimg.cn/img_convert/982ac80b565a826cd994f07e06dca771.png)\\n\\nBLOG_TOKEN生成指导：\\n1. [creating-a-personal-access-token-classic](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token#creating-a-personal-access-token-classic)不知道选那些权限选择的时候全部选上吧\\n![image](https://img-blog.csdnimg.cn/img_convert/62210a9d4b0a33a3e42c4e8cc79587fa.png)\\n\\n3. [creating-encrypted-secrets-for-a-repository](https://docs.github.com/cn/actions/security-guides/encrypted-secrets#creating-encrypted-secrets-for-a-repository)\\n\\n![image](https://img-blog.csdnimg.cn/img_convert/6456db8266e1e1b35ff515927567b9f7.png)\\n**另外还需要Pages仓库的权限配置**，如下图：\\n![image](https://img-blog.csdnimg.cn/img_convert/62626a0fa1949ae8208ed55ef8268025.png)\\n![image](https://img-blog.csdnimg.cn/img_convert/e76d03296df861b105a844b331546fee.png)\\n\\n### 自动发布文章到其他博客平台用来引流\\n- 发布到CSDN, 参考[issue2csdn](https://github.com/cloudswave/blog/blob/master/.github/workflows/issue2csdn.yml)增加workflows配置文件，[说明文档](https://github.com/cloudswave/issue2csdn#readme)\\n\\n### 提示\\n如果你只想在issue添加Miestone为publish的时候才发布更新，可以修改yml里的触发器，如：\\n```\\non:\\n  issues:\\n    # Sufficient to trigger this workflow when an issue is milestoned\\n    types: [milestoned]\\n    # types: [unlabeled, labeled, edited, milestoned]\\n```\\n\\n可以在Actions页面查看workflow执行日志和禁止某个workflow执行\\n<img width=\\\"805\\\" alt=\\\"action-guide\\\" src=\\\"https://user-images.githubusercontent.com/5915548/198864861-03313485-2a81-4f76-ba44-ec24254a619a.png\\\">\\n\\n## 参考文档\\n- https://github.com/Solybum/hexo-deploy\\n- https://github.com/zenghongtu/github-issue-to-hexo\\n- https://github.com/onblog/BlogHelper\\n-issue-to-hexo\\n- https://github.com/onblog/BlogHelper\\n- https://github.com/JoeyBling/yilia-plus-demo\\n\\n_本文同步于个人Github博客：[https://github.com/cloudswave/blog/issues/4](https://github.com/cloudswave/blog/issues/4)，欢迎留言。_\\n\",\"content\":\"<h2><a id=\\\"_0\\\"></a>背景</h2>\\n<ul>\\n<li>简书频繁下架文章，每天只能发布两篇</li>\\n<li>CSDN页面太杂，写作不够专注，客户端不好用</li>\\n<li>Github Web版和App都非常简洁实用，可以随意写文章不需要担心审核问题</li>\\n<li>可以通过Actions工作流来同步文章到Github Pages和其他博客平台</li>\\n<li>学习Github Action开发，学习机遇Github到自动化构建部署流程</li>\\n</ul>\\n<!--&#22686;&#21152;&#19979;&#38754;&#36825;&#20010;more&#27880;&#37322;&#20026;hexo&#25552;&#21462;&#26412;&#25991;&#25688;&#35201;&#38450;&#27490;hexo&#21338;&#23458;&#20027;&#39029;&#26174;&#31034;&#20840;&#25991;-->\\n<!--more-->\\n<h2><a id=\\\"_9\\\"></a>需求和实现</h2>\\n<h3><a id=\\\"README_10\\\"></a>自动生成README目录</h3>\\n<p>仿照<a href=\\\"https://github.com/cloudswave/blog/blob/master/.github/workflows/issue2readme.yaml\\\">.github/workflows/issue2readme.yaml</a>在仓库里增加workflows配置文件进行修改，<a href=\\\"https://github.com/bxb100/issueblog#readme\\\">说明文档</a>，最后生成<a href=\\\"https://github.com/cloudswave/blog/#readme\\\">效果</a></p>\\n<h3><a id=\\\"HexoGithub_Pages_13\\\"></a>自动构建Hexo发布到Github Pages</h3>\\n<p>仿照<a href=\\\"https://github.com/cloudswave/blog/blob/master/.github/workflows/issue2hexo.yaml\\\">.github/workflows/issue2hexo.yaml</a>在仓库里增加workflows配置文件，根据自身情况修改环境变量，最后发布的效果可查看<a href=\\\"cloudswave.github.io\\\">我的Hexo博客</a></p>\\n<pre><code>env:\\n  # github pages repo\\n  PUBLISH_REPOSITORY: cloudswave/cloudswave.github.io\\n  # GITHUB TOKEN 可以试一试将secrets.BLOG_TOKEN可以直接改成github内置的密钥secrets.BLOG_TOKEN，但是我的会报如下图的权限错误\\n  GITHUB_TOKEN: ${{ secrets.BLOG_TOKEN }}\\n</code></pre>\\n<p><img src=\\\"https://img-blog.csdnimg.cn/img_convert/982ac80b565a826cd994f07e06dca771.png\\\" alt=\\\"image\\\"></p>\\n<p>BLOG_TOKEN生成指导：</p>\\n<ol>\\n<li>\\n<p><a href=\\\"https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token#creating-a-personal-access-token-classic\\\">creating-a-personal-access-token-classic</a>不知道选那些权限选择的时候全部选上吧<br>\\n<img src=\\\"https://img-blog.csdnimg.cn/img_convert/62210a9d4b0a33a3e42c4e8cc79587fa.png\\\" alt=\\\"image\\\"></p>\\n</li>\\n<li>\\n<p><a href=\\\"https://docs.github.com/cn/actions/security-guides/encrypted-secrets#creating-encrypted-secrets-for-a-repository\\\">creating-encrypted-secrets-for-a-repository</a></p>\\n</li>\\n</ol>\\n<p><img src=\\\"https://img-blog.csdnimg.cn/img_convert/6456db8266e1e1b35ff515927567b9f7.png\\\" alt=\\\"image\\\"><br>\\n<strong>另外还需要Pages仓库的权限配置</strong>，如下图：<br>\\n<img src=\\\"https://img-blog.csdnimg.cn/img_convert/62626a0fa1949ae8208ed55ef8268025.png\\\" alt=\\\"image\\\"><br>\\n<img src=\\\"https://img-blog.csdnimg.cn/img_convert/e76d03296df861b105a844b331546fee.png\\\" alt=\\\"image\\\"></p>\\n<h3><a id=\\\"_35\\\"></a>自动发布文章到其他博客平台用来引流</h3>\\n<ul>\\n<li>发布到CSDN, 参考<a href=\\\"https://github.com/cloudswave/blog/blob/master/.github/workflows/issue2csdn.yml\\\">issue2csdn</a>增加workflows配置文件，<a href=\\\"https://github.com/cloudswave/issue2csdn#readme\\\">说明文档</a></li>\\n</ul>\\n<h3><a id=\\\"_38\\\"></a>提示</h3>\\n<p>如果你只想在issue添加Miestone为publish的时候才发布更新，可以修改yml里的触发器，如：</p>\\n<pre><code>on:\\n  issues:\\n    # Sufficient to trigger this workflow when an issue is milestoned\\n    types: [milestoned]\\n    # types: [unlabeled, labeled, edited, milestoned]\\n</code></pre>\\n<p>可以在Actions页面查看workflow执行日志和禁止某个workflow执行<br>\\n<img width=\\\"805\\\" alt=\\\"action-guide\\\" src=\\\"https://user-images.githubusercontent.com/5915548/198864861-03313485-2a81-4f76-ba44-ec24254a619a.png\\\"></p>\\n<h2><a id=\\\"_51\\\"></a>参考文档</h2>\\n<ul>\\n<li>https://github.com/Solybum/hexo-deploy</li>\\n<li>https://github.com/zenghongtu/github-issue-to-hexo</li>\\n<li>https://github.com/onblog/BlogHelper<br>\\n-issue-to-hexo</li>\\n<li>https://github.com/onblog/BlogHelper</li>\\n<li>https://github.com/JoeyBling/yilia-plus-demo</li>\\n</ul>\\n<p><em>本文同步于个人Github博客：<a href=\\\"https://github.com/cloudswave/blog/issues/4\\\">https://github.com/cloudswave/blog/issues/4</a>，欢迎留言。</em></p>\\n\\n\",\"readType\":\"public\",\"level\":\"1\",\"tags\":\"Top\",\"status\":0,\"categories\":\"\",\"type\":\"original\",\"original_link\":\"\",\"authorized_status\":false,\"Description\":\"背景\\n\\n简书频繁下架文章，每天只能发布两篇\\nCSDN页面太杂，写作不够专注，客户端不好用\\nGithub Web版和\",\"resource_url\":\"\",\"not_auto_saved\":\"1\",\"source\":\"pc_mdeditor\",\"cover_images\":[],\"cover_type\":\"0\",\"is_new\":1,\"vote_id\":0,\"pubStatus\":\"publish\"}",
  "method": "POST"
});
```
<img width="783" alt="截屏2022-10-30 下午2 36 30" src="https://user-images.githubusercontent.com/5915548/198865764-4e8ca3b5-417d-450b-adab-4e1bd2028d48.png">


---

<a id="issuecomment-1296136439"></a>
接下来的工作：
csdn创建文章的时候会返回文章id，然后记录到对应issue的某个地方，更新issue的时候通过该id去更新csdn文章而不重新创建