[CSDN博客接口](https://github.com/cloudswave/blog/issues/5)

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