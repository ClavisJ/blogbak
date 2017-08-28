title: Hexo 定制 Nlvi 主题
categories: Hexo
date: 2017-07-24 14:18:34
tags:
description:
---

之前一直使用的 `landscape-plus` 的主题，最近终于看腻了，来换一拨~

<!--more-->

### 一、选择及更换主题

博客主题可以在 [官方的主题页面](https://hexo.io/themes/) 里选择，或者参考知乎的一个排行榜，作为一个选择困难患者，在纠结了很久之后，最终选择了比较清爽的 [Nlvi](https://github.com/ColMugX/hexo-theme-Nlvi) 主题。

选择好之后，cd 到 `clavisBlog/themes/` 下，`clone` 下来并修改文件夹名字为 `Nlvi`，然后修改配置文件 `clavisBlog/_config.yml` 里面的 `theme` 为 `theme: Nlvi`

这个主题 `menu` 里面有一个 `about` 页面，需要把 `theme/Nlvi/_source` 里面的 `about` 拷贝到 `hexo/source/` 下面，这样替换主题就ok了 

### 二、开始修改主题

来看下 Nlvi 里面的结构

- _config.yml 主题的配置
- layout：页面的布局
- source：网页样式 css & js
- languages : 一些中英文对照

#### 修改导航栏

这里主题的默认导航如下

[![image1](http://7xstk7.com1.z0.glb.clouddn.com/hexo-customized-nlvi-theme-1.png)](http://7xstk7.com1.z0.glb.clouddn.com/hexo-customized-nlvi-theme-1.png)

在 `clavisBlog/themes/Nlvi/_config.yml` 里面注释或添加你想要的导航

```
menu:
  article: /
  archives: /archives
  # tags: /tags
  read: /read
  # about: /about
  # RSS: /atom.xml
```

这里我新增了 `read` 模块，需要在 `clavisBlog/themes/Nlvi/languages/default.yml` 和 `zh-CN.yml` 设置对应的中文对照

```
nav:
  read: Read
```

```
nav:
  read: 读书
```

相应的，`read` 模块需要像 `about` 一样，新增 `clavisBlog/source/read/index.md` 文件

```
title: read
type: "read"
```

之后需要添加 `read` 模块的样式文件 `clavisBlog/themes/Nlvi/source/style/_partial/read.styl`

```
.read
    padding (yard*2) (yard*7)
    font-family Athelas, "PingFang SC", serif
    font-weight 300
    .title
        font-family Athelas, "PingFang SC", serif
        font-weight 300
    .content
        padding (yard) (yard*2)

    p
        line-height line-height
    ul
        line-height 30px
```

并且在 `clavisBlog/themes/Nlvi/source/style/style.styl` 中导入样式

```
// 5. partial
@import "_partial/read"
```

样式配置好之后在 `clavisBlog/themes/Nlvi/layout/page.swig` 里面来设置页面布局

```
{% extends "_layout.swig" %}
    {% block title %}
        {{config.title}}
        {% if config.subtitle %}
            | {{ config.subtitle }}
        {% endif %}
    {% endblock %}

    {% block main %}
        {% if page.type === 'about' %}
            <div class="about syuanpi riseIn-light">
                <h2 class="title"> {{ __('nav.about') }} </h2>
                <div class="content"> {{ theme.self }} </div>
            </div>
        {% endif %}

        {% if page.type === 'read' %}
        <div class="read syuanpi riseIn-light">
            <h2 class="title"> {{ __('nav.read') }} </h2>
            <div class="content"> {{ theme.read }} </div>
            
        </div>
        {% endif %}

    {% endblock %}
```

最后在 `clavisBlog/themes/Nlvi/_config.yml` 里面配置 `read` 内容

```
read: <h3>2017</h3><ul><li>随遇而安</li><li>我敢在你怀里孤独</li><li>Objective-C 高级编程：iOS与OS X多线程和内存管理</li></ul>
```

自此，`read` 模块修改完成，这些都是全局搜索 `about` ，参照着来写的，效果如下

[![image-2](http://7xstk7.com1.z0.glb.clouddn.com/hexo-customized-nlvi-theme-2.png)](http://7xstk7.com1.z0.glb.clouddn.com/hexo-customized-nlvi-theme-2.png)

#### 设置页脚

在 `clavisBlog/themes/Nlvi/_config.yml` 中注释或添加你需要的页脚，这里我新增了一个 `mail`

```
sub:
#  facebook:
#  twitter:
#  instagram:
#  zhihu:
 github: ClavisJ
 weibo: /u/2833253020
 mail: ClavisJJ@gmail.com
 # linkedin:
```

接着在 `clavisBlog/themes/Nlvi/layout/_partial/_footer/contact.swig` 中去配置 `mail`，添加如下代码

```
<div class="contact-icon">
    {% set mail = 'mailto:' %}
    {% for name, path in theme.sub %}
        {% if name == 'mail' %}
            <a href="{{mail + path }}" target="_blank" class="iconfont icon-mail" title="{{ name }}"></a>
        {% endif %}
    {% endfor %}
</div>
```

页脚设置完毕

#### 设置博客主题色

`Nlvi` 的主题是灰色的有点单调，于是在 `clavisBlog/themes/Nlvi/source/style/_deploy/color.styl` 里面修改了色值，并修改 _config.yml 里面的 `theme-style: blue`

```
if $theme-style
    if $theme-style == "gray"
    else if $theme-style == "blue"
        $color-main = #7797BF
        $color-highlight = #FBB27A
        $color-border = #4F657F
        $color-back = #ebf3f5  
        $color-angle = #9FC9FF
        $color-line = #F5E8D1
        $color-hover = #FBB27A
```

配置成了蓝色基调的主题

[![image-3](http://7xstk7.com1.z0.glb.clouddn.com/hexo-customized-nlvi-theme-3.png)](http://7xstk7.com1.z0.glb.clouddn.com/hexo-customized-nlvi-theme-3.png)

这里安利一个 `Adobe` 炒鸡好用的 [选色网站](https://color.adobe.com/create/color-wheel/) ，可以通过拖拽选择相近的颜色或者补色

[![image-4](http://7xstk7.com1.z0.glb.clouddn.com/hexo-customized-nlvi-theme-4.png)](http://7xstk7.com1.z0.glb.clouddn.com/hexo-customized-nlvi-theme-4.png)

#### 一些页面细节样式设置

最后一些小的行高啊，色号啊边框之类的设置，在浏览器检查元素，拿到 `class`，全局搜索 `Nlvi` 里面的代码，都可以搞定，这里就不细说了

### 三、评论系统

之前的评论插件使用的多说，但多说在6月的时候已经挂掉了，在一番选择困难之后，最后选择 [gitment](https://github.com/imsun/gitment) 来作为博客的评论系统。作者有篇 [博客](https://imsun.net/posts/gitment-introduction/) 来介绍 `gitment` 的使用，这里就不再复述了。有一点要主要的是，最开始没理解到 `id` 那一行的意思，写了 `id:location.href`，导致评论对应的博客始终不对，最后查看了官方 `issue` 发现，直接不写那一行就可以了….

### 四、百度站长

登录百度站长，选择提交站点的时候，百度会让我们验证网站，并提供三种验证方式，最简单的一种是下载 HTML 验证文件，放在自己站点的根目录下，点击百度上的验证按钮即可。之前的一直没验证成功，才发现放在`clavisBlog/source/baidu_verify_D41CAOtccF.html` 的验证文件，在经过 `hexo generate` 之后，回加上主题的样式在文件里面，导致验证失败，所以，在 `hexo g` 命令之后，再在 `public` 文件夹下面加入 验证文件，再部署上去，这样就可以验证成功了~

这次对博客的配置便到这里＼(＾∀＾)メ(＾∀＾)ノ回见

##### 参考资料

[Hexo 优化：提交 sitemap 及解决百度爬虫无法抓取 GitHub Pages 链接问题](http://www.yuan-ji.me/Hexo-%E4%BC%98%E5%8C%96%EF%BC%9A%E6%8F%90%E4%BA%A4sitemap%E5%8F%8A%E8%A7%A3%E5%86%B3%E7%99%BE%E5%BA%A6%E7%88%AC%E8%99%AB%E6%8A%93%E5%8F%96-GitHub-Pages-%E9%97%AE%E9%A2%98/)