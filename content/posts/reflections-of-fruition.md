---
title: '关于 Fruition 问题的反思'
description: '免费终究还是最贵。'
date: '2021-08-19'
tags: ['博客', '技术']
---
在短短的一周之中，由于 Notion 方面的更改，Fruition 已经出现了两次较为严重的问题，这是值得反思的。虽然，在 Fruition 官方网站上已经有免责声明了，但是还是不得不感慨一下免费服务的不稳定性和第三方服务的不可控性。诚然，Fruition 所说的十分钟快速建站的确很方便，自己添加 Google Fonts 和其他代码也很不错，但是这毕竟不是官方支持下的行为，因此造成了这几天遇到的麻烦，可以说是一个巨大的教训，而且还会造成评论功能无法使用的尴尬状况。受到[这篇文章](https://www.notion.so/URL-e1620aa7a9204c289e0be7c65eaeef48)的启发，我打算使用跳转页面的方式来实现域名的缩短，而不是重写域名，也就是说我放弃了 Fruition 项目，但仍然可以使用 [picozai.tk](http://picozai.tk) 来访问本博客。

此外，为了保障可用性，我为本站做了一个[备份页面](https://static.picozai.tk)，使用的是 [Loconotion 项目](https://github.com/leoncvlt/loconotion) + Vercel + Github Actions。Loconotion 是我在东翻西找之后发现的一个比较令我满意的能够将 Notion 转化为静态页面的项目，类似的项目还有 [Nobelium](https://github.com/craigary/nobelium)、[Notablog](https://github.com/dragonman225/notablog) 等。然而，这些项目都不能保留网站的原貌，亦无法继承较为完整的 database 功能，这与我的需要相悖，故放弃。之所以使用这样的搭配，还有一个缘故是 GitHub 上有现成的利用 Actions 的[模板](https://github.com/artxia/Action-NotionSite)可以使用，免去了本地部署的麻烦而可以设置成定时拉取部署，不过这个模板有些许问题，问题由 Loconotion 导致，下一段细说。话又说回来，非常遗憾的是，Loconotion 并不支持切换 database 的视图，且对移动设备的支持欠佳，故不能用作主站。再谈到 Vercel，这是我最近发现的一个新鲜事物，但似乎早已不是什么小众的东西，通过与 Github 的绑定，Vercel 可以实现自动拉取代码部署的功能。经测试，用起来确实是比原生的 GitHub Pages 高速一些，而且这样一来就可以把项目仓库设置为私密，也无需担心公开仓库带来的隐私顾虑。

在调试 Loconotion 的时候，发现了一个奇怪的问题——子页面无法转换，根据报错信息，可以发现这是由于 Notion 方面改变了分享链接的格式导致的，然而灵活的解决方案并未找到，故直接把写死了的 www.notion.so 全部硬改为 pikt.notion.site ，然而这样还是有一些问题，就是在使用 Loconotion 的时候必须采取`python loconotion https://xxxx.notion.site/xxxx/{page-id}`的形式，而不能单纯写成`python loconotion https://xxxx.notion.site/{page-id}`，否则只有主页能被转化，子页会出错。为了让那个模板可以正常运作，我又自行 fork 了一遍 Loconotion 来修改，再通过`git submodules add xxxx.xxxx.xxxx`来链接到这个仓库（还是要点脸），除去上面的更改，我还把 Google Fonts 的地址更改为了国内的镜像地址以稍作加速。

总之，从本次的博客故障事件中，我还是学到了不少东西的，记录一下，也算是更新了点有意义的东西。