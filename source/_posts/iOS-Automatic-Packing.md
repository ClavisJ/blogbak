title: iOS使用脚本打包程序
categories: iOS
date: 2015-11-4 20:59:26
tags:
---

公司有个制作App平台的项目，让我研究自动打包技术，刚听到的时候，完全一脸懵逼，倒腾了几天，记录下自己

最初完全不知道，自动打包是个怎样的流程，听起来好高级的样子，后面才知道，实际上就是一堆终端命令来替代我们在Xcode的UI交互的打包步骤。

<!--more-->

### ipa到底是什么
> ipa是Apple程序应用文件iPhoneApplication的缩写。ipa 格式可以视为这种 .app 软件的衍生物。

>ipa 文件实质是一个 zip压缩，包含 3 个组件：payload 目录下的 .app 目录，这个是软件的主程序；iTunesArtwork，实质是一个无后缀名的 png 图片，用来在 iTunes 中显示图标；iTunesMetadata.plist，记录购买者信息、售价等数据。

>由于 zip 包不能记录权限和所有者等信息，所以苹果规定了 ipa 的安装方式，即全部 ipa 都会解包安装在 /var/mobile/Applications 目录下，全部文件和目录的所有者及用户组均设为 mobile(ID 为 501），主程序（可执行文件）的权限设为 0755 （所有人都可以执行，但只有所有者可以修改），可执行文件在 plist 中定义。全部目录权限设为 0755，而其它所有文件都设为 0644（仅所有者可以修改，其余人只允许读取，全部人都不允许执行）。

>所以对ipa修改的话会存在权限问题
>
>————摘自《百度百科》

### Xcode打包使用的工具
那我们用Xcode打包，简单的来说，就两个步骤

1. 打包实际上就是使用xcodebuild命令，将工程源文件编译成xxx.app
2. 使用xcrun命令 负责将xxx.app签名并打包成xxx.ipa

### 工程配置
这个项目的需求是这样的，在Web端设置程序名称，icon和启动页，选择应用模板，添加模块，最后点击打包按钮，得到ipa。因为打包的脚本里面包含的命令，必须使用Xcode支持，而这边的后台使用的是Windows，所以公司买了台Mac服务器，上面跑java程序来调用打包脚本命令。后面才知道，Mac服务器是用的黑苹果，打包的性能之低，并发超过5个就GG了😂

那么在打包之前还要做的是，修改程序的BundleID，替换icon和启动页

使用defaults write命令 可以对Plist的实现修改和添加

	defaults write plist路径  key -类型 值 
	defaults write /Users/Clavis/Desktop/TestProject/TestProject/Hello.plist OriginUserID -string 0000000

那么修改BundleID和BundleName也是同样的原理，代码如下

	defaults write /Users/rimi/Desktop/ArchiveTest/ArchiveTest/Info.plist "CFBundleIdentifier" "com.huachuang.archiveHello"
	defaults write /Users/rimi/Desktop/ArchiveTest/ArchiveTest/Info.plist "CFBundleName" "com.huachuang.archiveHello"

最开始还很要纠结BundleID对应的Key是什么，那Xcode中显示key去写，怎么都不对，其实，直接拿记事本打开info.plist就能看到对应的key到底是什么了。

#### 关于icon和loading的替换
这就直接使用mac的文件操作命令就ok

	cp file1 file2  拷贝文件
如果是替换整个文件夹，则加一个参数

	cp -R floder /Users/rimi/Desktop


### 具体代码
我们需要将这个终端命令整合在一起，使用的Shell脚本，就类似于Windows/Dos下的批处理命令。关于Shell的语法就不多说了，这里有个[入门教程](https://github.com/qinjx/30min_guides/blob/master/shell.md)

	#!/bin/sh

	#一、配置信息

	#工程目标名
	appName="TaoqiShow"  
	#工程目录
	diskDir="/Users/ClavisJ/Documents/TaoqiShow"  
	buildDir="build/Release-iphoneos"
	#签名证书
	signProfile="iPhone Distribution: XXXX  Co., Ltd" ### 这里写你自己证书的名称
	#ipa放置路径
	ipaPath="/Users/ClavisJ/Desktop/IPA"   
	
	# 本地测试数据
	newBundleName="xxxx"
	newBundleIdentifer="com.xxxx.testtttt"
	
	#二、修改工程信息
	
	#2.1 修改app显示名称和BundleID
	defaults write "${newCodeDir}/${appName}/Info.plist" "CFBundleIdentifier" $newBundleIdentifer
	defaults write "${newCodeDir}/${appName}/Info.plist" "CFBundleDisplayName" $newBundleName


	#2.2 替换图片资源文件夹  参数1是旧的目录  参数2是新的目录
	cp $newIconPath ${newCodeDir}/TaoqiShow/images 


	# #三、执行打包命令

	#删除build文件
	rm -rdf "${diskDir}/build"
	
	# #1. 切换到工程目录下
	 cd "${newCodeDir}"
	
	# #2. clean工程
	 xcodebuild -target $appName clean
	
	# #3. 打包生成app (不加签名的代码 xcodebuild -target $appName)
	 xcodebuild -target $appName -configuration Distribution -sdk iphoneos CODE_SIGN_IDENTITY="$signProfile"
	
	# #4. 将app打包生成ipa
	 xcrun -sdk iphoneos PackageApplication -v "${newCodeDir}/${buildDir}/${appName}.app" -o "${ipaPath}/${newUserID}/Taoqo_${newUserID}.ipa"
	 cd "${ipaPath}"
	 ls

使用的话，复制上面代码，保存为.sh的脚本，改下里面的参数，先提高shell文件的权限`chmod 777 /Users/ClavisJ/Desktop/Pack.sh`,然后运行`sh /Users/ClavisJ/Desktop/Pack.sh`,不加sh也ok。shell接收参数的话，定义个变量`newAppName=$1`,再运行时传参`sh /Users/ClavisJ/Desktop/Pack.sh Parameter1 Parameter2`这样

### Tips
1. 有些小伙伴可能会遇到，每次打包的时候都会问你要证书的访问权限，但是，既然是自动打包，终不能老是手动点同意啊。这个问题纠结了我好久，最后其实解决方案很简单，在keychain里面找到你的证书，展开，点击私钥的右键 -> 显示简介 -> 访问控制设置成允许所有项目访问
   ![](http://7xstk7.com1.z0.glb.clouddn.com/KeyChainCertificateAuthority)
2. 以上脚本针对的打包路径是默认路径，有些Xcode配置可能不一样，你得设置一下，或者根据你的配置改改脚本。
   设置路径：Xcode menu > Preferences... item > Locations tab > Locations sub-tab > Advanced... button > Custom option.
   ![](http://7xstk7.com1.z0.glb.clouddn.com/XcodeBuildSetting.png)
3. 虽然可以直接用命令修改BundleName等，但不知道是不是编码方式什么影响的，在java程序调用Shell脚本传参重写BundleName时，在Shell中打印出接收的参数都是正确的，最后写入的结果确总是乱码，所以最后还是拿java重新生成了一份info.plist来替换源码中的那一份╮(╯▽╰)╭

### 不足的地方
这份脚本没有改描述文件，所以还得你程序手动打包ok，才能保证自动打包不出错，一些参数在脚本中配置也麻烦，而且打包的一些细节也没配置，还有静态库动态库框架什么的，等后面抽点时间，写个小的Mac程序吧:)

### 参考资料

>[iOS自动打包并发布脚本](http://liumh.com/2015/11/25/ios-auto-archive-ipa/)
>
>[xcode_shell](https://github.com/webfrogs/xcode_shell)
>
>[xcodebuild和xcrun实现自动打包iOS应用程序](http://3426724.blog.51cto.com/3416724/883484)

