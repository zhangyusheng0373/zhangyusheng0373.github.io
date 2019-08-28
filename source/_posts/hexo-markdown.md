---
title: hexo markdown语法及标签使用
date: 2018-06-21 11:00:43
categories: "博客搭建"
tags: 
  - hexo 
  - markdown
toc: true
---
{% note success %} ### 须知 {% endnote %}

### 为什么要写这篇文章  
　　最近搭建了hexo的静态博客,想着文章用markdown写挺方便的.但发现使用过程中,自己写的md文件生成的博客总是与本地软件中预览的不一致(包括有道云笔记,MarkdownPad等编辑器).然后想着能不能自己总结下 hexo 的markdown语法呢?哪些可用哪些不可用? 哪些是主题自带的标签,哪些是hexo内置的标签?
　　于是这边文章诞生了.长期更新ing...
  
{% note success %} ### markdown基本语法 {% endnote %}
#### 换行
　　1,hexo下,直接回车键就可以换行.无需在行尾加上两个空格键再回车.
{% blockquote %}
行尾没有空格
测试换行
{% endblockquote %}
　　**但是标准的markdown写法是要在行尾加两行空格再回车,否则不会换行.**

　　2,公用方法:在要换行文字前面加上br标签.
#### 首行缩进
　　一般的中文输入法都是按shift+space，可以切换到全角模式下，这时一个空格的宽度是整整一个汉字。故输入两个全角状体下的空格（space）即可。
注意输入完后要切换回（再次按 shift + space）正常输入状态。

{% note success %} ### hexo标签 {% endnote %}
#### 引用块
**用途:**在文章中插入引言,在markdown中,使用>或者``<blockquote>`` ``</blockquote>``标签对来表示,在hexo中丰富了功能,如可包含作者,来源和标题.
**标签(使用时注意去掉{与%间的空格):** { % blockquote [author[, source]] [link] [source_link_title] %} { % endblockquote %}

**例子:** 
{% blockquote %}
{ % blockquote 崔健 https://y.qq.com/n/yqq/song/003ce6td37rQIU.html?ADTAG=baiduald&play=1 花房姑娘 %} 
你要我留在这地方
你要我和它们一样
我看着你默默地说
噢 不能这样
{ % endblockquote %}
{% endblockquote %}
页面效果:
{% blockquote 崔健 https://y.qq.com/n/yqq/song/003ce6td37rQIU.html?ADTAG=baiduald&play=1 花房姑娘 %} 
你要我留在这地方
你要我和它们一样
我看着你默默地说
噢 不能这样
{% endblockquote %}

#### 代码块
**用途:**在文章中插入代码.
**标签(使用时注意去掉{与%间的空格):**{ % codeblock [title] [lang:language] [url] [link text] %} { % endcodeblock %}
**例子:**
{% blockquote %}
{ % codeblock 测试代码 lang:python https://www.python.org/ Python %}
class Test:
	def hello(self):
		print("hello world!")

if __name__ == "__main__":
	a = Test()
	a.hello()
{ % endcodeblock %}
{% endblockquote %}
页面效果:
{% codeblock 测试代码 lang:python https://www.python.org/ Python %}
class Test:
	def hello(self):
		print("hello world!")

if __name__ == "__main__":
	a = Test()
	a.hello()
{% endcodeblock %}

#### Pull Quote
**用途:** 在文章中插入 Pull Quote
{% pullquote color=red %}
content
{% endpullquote %}

#### 插入图片
**用途:**在文章中插入图片.
**标签(使用时注意去掉{与%间的空格):**{ % img [class names] /path/to/image [width] [height] [title text [alt text]] %}
**例子:**
{% blockquote %}
{ % img https://gss0.baidu.com/-fo3dSag_xI4khGko9WTAnF6hhy/zhidao/pic/item/2fdda3cc7cd98d10af7bfd9c293fb80e7aec90f4.jpg 1600 567 MUJI 地平线系列广告 %}
{% endblockquote %}
页面效果:
{% img https://gss0.baidu.com/-fo3dSag_xI4khGko9WTAnF6hhy/zhidao/pic/item/2fdda3cc7cd98d10af7bfd9c293fb80e7aec90f4.jpg 1600 567 MUJI 地平线系列广告 %}


### NexT 内置标签
#### 引用块 居中
**用途:**优化引用块显示,并居中文本
**标签(使用时注意去掉{与%间的空格):**三种方法.
{% codeblock %}
# HTML方式: 直接在 Markdown 文件中编写 HTML 来调用
# 其中 class="blockquote-center" 是必须的
<blockquote class="blockquote-center"> </blockquote>

# 标签 方式，要求版本在0.4.5或以上
{ % centerquote %}{ % endcenterquote %}

# 标签别名
{ % cq %} { % endcq %}
{% endcodeblock %}

**例子:**
{% blockquote %}
{ % cq %}
你要我留在这地方
你要我和它们一样
我看着你默默地说
噢 不能这样
{ % endcq %}
{% endblockquote %}
页面效果:<br>
{% cq %}
你要我留在这地方
你要我和它们一样
我看着你默默地说
噢 不能这样
{% endcq %}
<br>
#### 图片放大
**用途:**当使用此标签引用图片时，图片将自动扩大 26%，并突破文章容器的宽度。 此标签使用于需要突出显示的图片, 图片的扩大与容器的偏差从视觉上提升图片的吸引力。
**标签(使用时注意去掉{与%间的空格):**三种方法.
{% codeblock %}
# HTML方式: 直接在 Markdown 文件中编写 HTML 来调用
# 其中 class="full-image" 是必须的
<img src="/image-url" class="full-image" >

# 标签 方式，要求版本在0.4.5或以上
{ % fullimage /image-url, alt, title %}

# 别名
{ % fi /image-url, alt, title %}
{% endcodeblock %}

**例子:**
{% blockquote %}
``<img src="https://gss0.baidu.com/-fo3dSag_xI4khGko9WTAnF6hhy/zhidao/pic/item/2fdda3cc7cd98d10af7bfd9c293fb80e7aec90f4.jpg" class="full-image" >``
{% endblockquote %}
页面效果:<br>
<img src="https://gss0.baidu.com/-fo3dSag_xI4khGko9WTAnF6hhy/zhidao/pic/item/2fdda3cc7cd98d10af7bfd9c293fb80e7aec90f4.jpg" class="full-image" >
<br>
#### 笔记标签
**用途:**类似引用块.
**标签(使用时注意去掉{与%间的空格):**
* { % note class_name %} { % endnote %}
* 其中，class_name 可以是以下列表中的一个值：
* default
* primary
* success
* info
* warning
* danger
**例子:**
{% blockquote %}
{ % note info %} info显示效果 { % endnote %}
{% endblockquote %}

<br><br><br>
