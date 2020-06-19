---
title: gitalk 自动初始化
date: 2020-05-07T11:08:34+08:00
tags: [gitalk]
categories: [gitalk]
---





​	前面有篇文章介绍了使用`hexo`的评论系统`gitalk`,但是使用过程中有一些小问题,每一篇文章都需要初始化一下`issues`,不然是无法评论的,如果文章太多,一个一个初始化就太浪费时间和精力,所以借鉴了网上的一些资料完成一个自动化初始评论的需求

<!--more-->

1. 首先在根目录下新建一个`gitalk.init.js`文件
2. 需要一些`node`的依赖包,可以自己安装
   1.  `npm install cheerio --save`
   2. `npm install request --save`
   3. `npm install xml-parser --save`
   4. `npm install yamljs --save`
3. 复制一下代码

```nodejs
const request = require("request");
const fs = require("fs");
const path = require("path");
const url = require("url");
const xmlParser = require("xml-parser");
const YAML = require("yamljs");
const cheerio = require("cheerio");
const crypto = require('crypto');
// 根据自己的情况进行配置
const config = {
    username: "name", // GitHub 用户名
    token: "token",  // GitHub Token
    repo: "blogtalk",  // 存放 issues的git仓库
    // sitemap.xml的路径，gitalk.init.js放置在根目录下，无需修改，其他情况自行处理
    sitemapUrl: path.resolve(__dirname, "./public/sitemap.xml"),
    kind: "Gitalk",  // "Gitalk" or "Gitment"
};
let issuesUrl = `https://api.github.com/repos/${config.username}/${config.repo}/issues?access_token=${config.token}`;

let requestGetOpt = {
    url: `${issuesUrl}&page=1&per_page=1000`,
    json: true,
    headers: {
        "User-Agent": "github-user"
    }
};
let requestPostOpt = {
    ...requestGetOpt,
    url:issuesUrl,
    method: "POST",
    form: ""
};

console.log("开始初始化评论...");

(async function() {
    console.log("开始检索链接，请稍等...");
    
    try {
        let websiteConfig = YAML.parse(fs.readFileSync(path.resolve(__dirname, "./_config.yml"), "utf8"));
        
        let urls = sitemapXmlReader(config.sitemapUrl);
        console.log(`共检索到${urls.length-1}个链接`);
        
        console.log("开始获取已经初始化的issues:");
        let issues = await send(requestGetOpt);
        console.log(`已经存在${issues.length}个issues`);
        
        let notInitIssueLinks = urls.filter((link) => {
            return !issues.find((item) => {
                link = removeProtocol(link);
                return item.body.includes(link);
            });
        });
        
        for(let i=0;i<notInitIssueLinks.length;i++)
        {
            if(notInitIssueLinks[i].endsWith("tags/index.html"))
            {
                notInitIssueLinks.splice(i,1);
                i--;
            }
        }

        if (notInitIssueLinks.length > 0) {
            console.log(`本次有${notInitIssueLinks.length}个链接需要初始化issue：`);
            console.log(notInitIssueLinks);
            console.log("开始提交初始化请求, 大约需要40秒...");
            /**
             * 部署好网站后，直接执行start，新增文章并不会生成评论
             * 经测试，最少需要等待40秒，才可以正确生成， 怀疑跟github的api有关系，没有找到实锤
             */
            setTimeout(async ()=>{
                let initRet = await notInitIssueLinks.map(async (item) => {
                    let html = await send({ ...requestGetOpt, url: item });
                    let title = cheerio.load(html)("title").text();
                    let desc = item + "\n\n" + cheerio.load(html)("meta[name='description']").attr("content");
                    let pathLabel = url.parse(item).path;
                    let label = crypto.createHash('md5').update(pathLabel,'utf-8').digest('hex');
                    let form = JSON.stringify({ "body": desc, "labels": [config.kind, label], "title": title });
                    return send({ ...requestPostOpt, form });
                });
                console.log(`已完成${initRet.length}个！`);
                console.log("可以愉快的发表评论了！");
            },40000);
        } else {
            console.log("本次发布无新增页面，无需初始化issue!!");
        }
    } catch (e) {
        console.log(`初始化issue出错，错误如下：`);
        console.log(e);
    } finally {
    
    }
})();

function sitemapXmlReader(file) {
    let data = fs.readFileSync(file, "utf8");
    let sitemap = xmlParser(data);
    return sitemap.root.children.map(function (url) {
        let loc = url.children.filter(function (item) {
            return item.name === "loc";
        })[0];
        return loc.content;
    });
}

function removeProtocol(url) {
    return url.substr(url.indexOf(":"));
}

function send(options) {
    return new Promise(function (resolve, reject) {
        request(options, function (error, response, body) {
            if (!error) {
                resolve(body);
            } else {
                reject(error);
            }
        });
    });
}
```

3. 通过使用`nodejs`运行这个`js`脚本

`node ./gitalk.init.js`



----

注意事项:

因为涉及到了`token`在`push`后会警告,所以将仓库设置为了私有,`token`如果不可用就在生成一个`token`在个人设置里的开发设置里

所以在每次添加新文章时都要运行脚本,为了方便可以使用计划任务,参考上篇文章

最好分开设置计划任务,按时间顺序

```
//先设置gitpull的任务,
cd /root/git/resp_name/ && git pull
//npm install 可选,一般不新增功能不回用到,有时新增了依赖,git pull会出错,先删除 package-lock.json,在git pull 然后 npm install
//开启hexo服务
cd /root/git/resp_name/ && npm run deploy
//最后初始化评论
cd /root/git/resp_name/ && node ./gitalk.init.js
```

如图:

![1588821829617](/images/hexo-gitalk-init/1588821829617.png)

时间,脚本根据自己的意愿进行设置,可以频繁也可以时间跨度大些,我们只需要写文档,然后`push`到`github`,也算是实现了自动化吧
