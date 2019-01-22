---
title: 'GitCafe 解决百度爬虫无法爬取 GitHub 个人博客问题'
layout: post
tags:
    - GitCafe
    - GitHub
    - 百度爬虫
---
### GitHub 禁止百度爬虫
![curl -A 模拟百度爬虫]({{site.url}}/media/img/YXU$6%5BPQ_G%25I%5DWF~%7D@~%60SY8.jpg)
通过上图发现，百度爬虫是无法爬取到 GitHub 上的内容的。
想用 GitHub 托管个人博客，同时又想被百度爬虫爬取到的筒子有以下方法：

* 让 GitHub 改，呵呵...
* 利用 CDN 加速,有一定的可行性，但不完美。 

> 因为使用 CDN 的原理是让 BaiduSpider 访问 CDN 的代理服务器，而不直接向 Github 发送请求。假设你有多个 CDN 节点，但是离 BaiduSpider 较近的节点没有缓存，爬虫就会去 Github 请求，得到一个403。所以前提是要保证所有的节点都有缓存，因此只有当你的站点访问量较大时效果会比较好。

* 镜像服务器

### GitCafe 镜像服务器
我使用[gitcafe](https://coding.net/gitcafe.html)（国内的github）作为我的 Blog 镜像服务器。操作非常方便，可参考 GitCafe 官方文档[ Coding Pages 介绍](https://coding.net/help/doc/pages/index.html)。

配置域名解析。

![域名解析]({{site.url}}/media/img/F29VMUNYODSRYYO9KJF8PSE.jpg)
国内用户访问 GitCafe，国外用户访问 GitHub。访问速度可提升不少。

在 GitHub 工作目录中编辑完内容之后需要将代码一并 Push 到 GitCafe 远端仓库。

有两种方法：

1. 启用 GitHub 的 Webhooks，每当有 Push 到 GitHub 时，GitHub 发送一个请求到一个主机上，主机抓取GitHub上更新的内容并同步到 GitCafe。然后再把域名解析到 GitCafe。这种方式比较 Geek(方便)
2. 添加一个 GitCafe 远端地址，每次多 Push一次到 GitCafe。

```
git remote add gitcafe git@git.coding.net:mythsumm/mythsumm.git
git push
git push gitcafe gh-pages:coding-pages 
```
这种方式比较容易实现。