title: 升级 CocoaPods 到1.0.1
categories: iOS
date: 2016-07-13 13:47:29
tags:
description:
---

Pods发布了 1.0 啦，虽然现在的 0.39 也能用，不过想尝尝鲜，来记一波升级步骤

<!--more-->

#### 环境信息
OS X El Capitan 10.11.5

## 1.切换源

	gem sources -r https://rubygems.org/
	gem sources -a https://gems.ruby-china.org/

	$ gem sources -l
	*** CURRENT SOURCES ***

	https://gems.ruby-china.org/


## 2.在线更新 gem

在线自动更新的命令如下:

	$ sudo gem update --system
	Password:
	Updating rubygems-update
	Fetching: rubygems-update-2.6.6.gem (100%)
	ERROR:  While executing gem ... (Errno::EPERM)
	    Operation not permitted - /usr/bin/update_rubygems

我更新失败了，好像是由于`EL Capitan`系统限制了写入权限，要调整有点麻烦，再来试下指定版本更新，结果也失败了😂

	$ sudo gem update --system 2.6.1
	Password:
	Updating rubygems-update
	Fetching: rubygems-update-2.6.1.gem (100%)
	ERROR:  While executing gem ... (Errno::EPERM)
	    Operation not permitted - /usr/bin/update_rubygems
   

## 3.手动更新 gem
 
那既然在线不行，于是我尝试手动更新：

1. 在这里[下载](https://rubygems.org/pages/download#formats)
2. 解压并cd到对应目录
3. 用命令`sudo ruby setup.rb`进行安装
4. 最后你可以运行`$ gem -v`来查看下版本，我的是2.6.6 

更新成功！  
    

## 4.更新 CocosPods

接着来更新 CocosPods， 可以运行和安装时一样的命令：

	$ gem install cocoapods

然而，我还是报错：

	$ gem install cocoapods
	ERROR:  While executing gem ... (Gem::RemoteFetcher::UnknownHostError)
	    no such name (https://ruby.taobao.org/quick/Marshal.4.8/cocoapods-1.0.1.gemspec.rz)

在浏览器能访问这个地址，还能下载，我真的是...... 然后继续折腾，不知道是不是源的问题，尝试指定源安装：

	gem install cocoapods --source http://rubygems.org

还是失败= =|||.... 不知道是不是源有问题，查到 Ruby 社区里面有篇[帖子](https://ruby-china.org/topics/13086)，提到了一个有点不同的加源命令，继续try

	$ gem sources --remove https://ruby.taobao.org/
	https://ruby.taobao.org/ removed from sources
	ClavisJ-Pro:~ ClavisJ$ gem sources --add https://ruby.taobao.org/ -V
	Getting SRV record failed: DNS result has no information for _rubygems._tcp.ruby.taobao.org
	GET https://ruby.taobao.org/specs.4.8.gz
	302 Moved Temporarily
	GET https://rubygems-china.oss-cn-hangzhou.aliyuncs.com/specs.4.8.gz
	200 OK
	https://ruby.taobao.org/ added to sources


其实两个安装源是一样的，帖子中有说网络问题，我把VPN断咯，再来继续指定更新 cocoaPods，就不报访问不到的Error了

	$ sudo gem update cocoapods
	Password:
	Updating installed gems
	Updating cocoapods
	Fetching: cocoapods-core-1.0.1.gem (100%)
	Successfully installed cocoapods-core-1.0.1
	Fetching: cocoapods-deintegrate-1.0.0.gem (100%)
	Successfully installed cocoapods-deintegrate-1.0.0
	Fetching: cocoapods-downloader-1.1.0.gem (100%)
	Successfully installed cocoapods-downloader-1.1.0
	Fetching: cocoapods-plugins-1.0.0.gem (100%)
	Successfully installed cocoapods-plugins-1.0.0
	Fetching: cocoapods-search-1.0.0.gem (100%)
	Successfully installed cocoapods-search-1.0.0
	Fetching: cocoapods-stats-1.0.0.gem (100%)
	Successfully installed cocoapods-stats-1.0.0
	Fetching: cocoapods-trunk-1.0.0.gem (100%)
	Successfully installed cocoapods-trunk-1.0.0
	Fetching: cocoapods-try-1.1.0.gem (100%)
	Successfully installed cocoapods-try-1.1.0
	Fetching: molinillo-0.4.5.gem (100%)
	Successfully installed molinillo-0.4.5
	Fetching: cocoapods-1.0.1.gem (100%)
	ERROR:  While executing gem ... (Errno::EPERM)
	    Operation not permitted - /usr/bin/pod
    
这次能Fetch到安装需要的东西了，但是卡在了最后一步，还是没有写入权限，看来权限这个坑是绕不过去了，继续查资料，最后终于在[stackoverflow](http://stackoverflow.com/questions/33077977/cocoa-pods-not-updating-pods-on-el-capitan)上找到解决方案咯，如下

	$ sudo gem install -n /usr/local/bin cocoapods
	Successfully installed cocoapods-1.0.1
	Parsing documentation for cocoapods-1.0.1
	Installing ri documentation for cocoapods-1.0.1
	1 gem installed

看到安装成功的提示，简直太开心了，真的是好折腾，嘛，生命不息，折腾不止！~\(≧▽≦)/~


