---
title: "使用AWS给GitHub Blog加上HTTPS"
date: 2018-07-08T21:28:53+08:00
lastmod: 2018-07-08T21:28:53+08:00
draft: false
keywords: [aws, cloudfrount, acm, https, github, blog]
description: ""
tags: [aws, cloudfrount, acm, https, github, blog]
categories: [https, github, blog]
---

虽然用GitHub当做自己的Blog也没有写几篇内容，但没事了折腾一下Theme（虽然很丑，我是当不了一个好的前端了），留言（虽然到目前为止也没有什么人留言，#_#!）啊什么的还是挺爽的。今天突然难耐不住，想给它加上HTTPS，大家伙都知道，GitHub因为种种原因，不是很方便的能提供这样的服务。

要干这样的事情，唯一能走的路子就是在前面再加上一层代理了，刚开始考虑使用AWS ALB的，因为据说它后端可以支持外部资源了，但是大概瞄了一下，发现这个基本上算是一个误传，目前来说，还是只能支持VPC内的Instance或者私网IP。嗯，私网IP。

好吧，次优方案就是CloudFront了，闲话少说。因为想继续使用`blog.chatops.in`作为访问入口的，所以第一件事情就是准备一个origin的域名，登录去他爹(godaddy)的网站新增一条CNAME记录到我的github的地址，另外记得更新GitHub中的CNAME文件到这个新的域名上。第二件事情就是到ACM申请一个证书了，这个没有什么难度，照着流程走。这个过程中有个地方把自己小坑了一把，因为使用了域名验证，AWS提供的文档中是全域名的，没有多想就贴到去他爹的表单中，结果等了好一会也没有通过，自己本地解析也是失败的，才想起来去他爹那里是不需要自己的Apex那部分的。真的是去他爹的坑爹啊。

接下来就是去配置一个CloudFront节点了，在Origin那里输入上面准备的新的origin的域名，选择使用自定义的证书(Custom SSL Certificate)，贴上上一步在ACM中申请的证书的ARN就可以了，另外在访问者策略(Viewer Protocol Policy)那里选上转发HTTP到HTTPS(Redirect HTTP to HTTPS)，其他大部分的地方保持默认，诸如什么安全策略的版本，支持的HTTP的版本，部署的范围之类的，随个人爱好和经济情况定了。这里唯一要说一下的是这玩意的部署和更新真的很慢，需要极大的耐心。

在CloudFront部署完成后，当你能正常解析CloudFront的域名之后(类似于d3bmkjusa7yq2q.cloudfront.net这样的)，就需要到去他爹那里把入口的域名更新到这里了，等域名更新完成之后，再次访问一下，抬头看看地址栏是不是有小绿锁了。如果是的，那恭喜你。如果不是，嗯~回头再去想一想是不是哪一步忘了点什么，祝你好运。

在更新域名的过程中，发现去他爹现在提供了对Apex记录的Forwarding功能，所以我把自己的转发到Blog上面了，oh~yeah。