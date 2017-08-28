title: Mac平台下如何搭建Hexo博客
date: 2015-11-1 21:08:13 
categories: Hexo
---

## 前言

终于搭建好了自己的博客，在此分享下建站的全过程，希望能对大家有所帮助。

<!--more-->

#### 环境信息
Mac 10.10.5 Xcode 7.0.1

# 一、为什么选择Hexo
前几年搭建博客的话，大家第一选择都是使用WordPress，而当我自己搭建的时候才发现，现在有一堆博客框架可以选择，Octopress、Ghost、Kelly、Jelly、Hexo等等等！天啊噜，我严重觉得自己有选择恐惧症。

除了稍微用过wordPress外，其余的东西我并没有用过，于是查咯好多资料纠结选哪个博客。这些博客可以简单的分为两类，需要服务器的，不需要服务器的，像WordPress，Ghost这样就是需要服务器的。而其他都是一堆静态博客，Jelly(像黑客一样写博客)，Octopress（Jekyll的再开发）, Hexo（Node.js写的，编译速度比前两者快），本质都是一样的，如果你还想深入的体会各个之间的区别，建议还是自己都用一下才比较明了。

最后选择了Hexo，主要是因为感觉会geek一点（不要笑），而且编译速度快，是Node.js写的，感觉会比较亲切，支持MarkDown，并且生成静态页面就懒得搞后台了。

# 二、安装Hexo
那么来讲讲Hexo的安装，他需要Git和Node.js的支持，再用nvm来安装hexo。

### 1.安装Git
我是直接安装的Xcode，Xcode安装完成之后会自带终端和Git，可以代码`git --version`查看Git版本。

### 2.安装Node.js
安装Node.js有多种方法，由于Mac自带ruby，可以打算安装homebrew（专门用于安装open source）工具，再用homebrew安装Node.js，但是我找了好多homebrew的连接都是404= =|||，后来就放弃这个方法了，而是直接从github上面拷贝下来再安装。

```
git clone git://github.com/joyent/node.git
cd node
./configure
make
sudo make install
```

从github上面拷贝下来之后记得查看下当前的分支是不是在master上，不是的话记得切换分支。

### 3.安装Hexo

使用npm工具安装Hexo

```  
sudo npm install -g hexo
sudo npm install --unsafe-perm --verbose -g hexo
```
这里的第一句命令要么加上-g是全局安装，去掉-g是把模块安装到当前目录，全局安装是安装到root目录下的，在root目录下安装文件都需要root权限，所以要加上sudo。那么至此Hexo博客就搭建完成了！

# 三、Hexo基本配置及使用

关于Hexo的目录结构以及查看完整的配置的话，还是建议到[官网](https://hexo.io/docs/)上去看，英文不好的同学可以点击[这里](http://www.liuzhixiang.com/hexo_site_cn/docs/index.html)。

### 1.初始化Hexo

```
hexo init <folderPath>
```
也可以cd到目标目录，然后执行`hexo init`。

### 2.生成静态页面
```
hexo generate
```
命令必须在init目录下执行

### 3.本地启动Hexo
```
hexo server
```
这时大家访问`http://localhost:4000`就可以看到自己的网站啦！

### 4.写文章

```
hexo new [layout] "postName" #新建文章
```
其中layout是可选参数，默认值为post,其余layout在scaffolds中查看。

### 5.关联GitHub

需要注册一个GitHub账号，然后创建一个repository，注意仓库名称是特定的，比如我的GitHub账号是ClavisJ，那么对应的仓库名称是ClavisJ.github.io。

然后编辑_config.yml文件，修改最后的deploy的配置，将仓库地址换成你自己的地址。tip：这里的配置和Hexo的版本有关，3.0前后的会有一些差别，大家注意一下。

```
deploy:
  type: git
  repository: https://github.com/ClavisJ/clavisj.github.com.git
```

将下来就可以执行部署命令了(tip:有些新用户可能需要配置ssh，可以查看[官方配置说明](https://help.github.com/articles/generating-ssh-keys/))

```
hexo clean  # 发布前最好清理一下
hexo generate
hexo deploy
```
现在访问clavisj.github.io就可以看到你自己搭建的博客了。

# 四、小结

总的来说搭建Hexo博客还是比较简单的，用起来也比较顺手，大家有兴趣可以自己搭建一个，想要独立的域名的话，也可以在Godaddy上面买一个，还可以用个图床存储图片，将博客从GitHub移到GitCafe上提升访问速度，做做SEO优化，过滤下垃圾评论，做下RSS订阅，自己修改修改主题什么的，这些后期实现了的话在来和大家分享吧: )

当然搭建博客，选择什么博客平台都不是重点，最重要的是要自己坚持写作，不断分享干货，不断提升自己技术，与君共勉。


### 参考资料

[hexo你的博客](http://ibruce.info/2013/11/22/hexo-your-blog/)

[如何搭建一个独立博客——简明Github Pages与Hexo教程](http://cnfeat.com/blog/2014/05/10/how-to-build-a-blog/)
