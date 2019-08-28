---
title: hexo NexT配置algolia报错问题归档
date: 2018-06-14 16:00:43
categories: "博客搭建"
tags: 
  - hexo
toc: true
---
![Banner](/images/NexT.png)
NexT官方主题配置：http://theme-next.iissnan.com/third-party-services.html#algolia-search
{% note success %} # 踩坑一：配置文件格式不正确 {% endnote %}
1、按照官方配置Algolia搜索后，在git bash上执行hexo algolia报错:<br>
{% blockquote %}
ERROR [Algolia] Please provide an Algolia index name in your hexo _config.yml file.
ERROR >> Read https://npmjs.com/hexo-algolia#public-facing-search-options for more informations.
{% endblockquote %}
报错内容提示找不到index name，检查_config.yml 的确配上了。见下图，
![_config.yml配置](https://upload-images.jianshu.io/upload_images/2691689-a609d684d7b57127.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

后来发现，配置项前要加上缩进。再次修改后，再次执行hexoalgolia就可以成功搭建啦。
![hexo_config.png](https://upload-images.jianshu.io/upload_images/2691689-42c36b832a582c8b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

{% note success %} # 踩坑二：配置文件缺失{% endnote %}
部署好之后，点击页面上的搜索无响应。查看控制台，发现报错,设置无效:
{% blockquote %}
Algolia Settings are invalid.
{% endblockquote %}
再次检查配置发现和官方要求的配置项一样。度娘后，发现竟然还要加入apiKey和adminApiKey。才能正常使用。
![hexo_config_a.png](https://upload-images.jianshu.io/upload_images/2691689-60baea3784cb1d15.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
加上后再部署下。点击页面搜索，大功告成。