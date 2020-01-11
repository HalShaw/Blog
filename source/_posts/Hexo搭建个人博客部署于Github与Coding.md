---

title: Hexo搭建个人博客部署于Github与Coding
date: 2016-08-14 16:07
tags: Hexo Blog
categories: Hexo
---

- 早就听说可以自己弄一个博客然后免费部署到github上面，但是一直没机会着手去做，前几天，机缘巧合之下，发现了[Hexo](https://www.npmjs.com/package/hexo)这个好东西，这才发现原来它就是我苦苦找寻的的东西啊。真是“**踏破铁鞋无觅处，得来全不费功夫。**”啊。
- 那好，lz要开始搭建个人博客啦（入坑了）。期间各种坑只能自己体会，虽然外面已经很多搭建的教程了，但是我也来凑个热闹，其实也算是记录一下自己学习的过程，还有期间所遇的各种坑以至于不得不中断到改日再战的坑，在次记录一下让后面的小伙伴们更方便一些，不至于那么惨。
- 首先必须**安装一下[Git](https://git-scm.com/downloads) for windows**,然后申请个[GitHub](https://github.com/)账号啊，建议日后想从事CS相关的童鞋多上上全球最大的同性交友网站（GitHub），里面的好东西真不少，受益匪浅啊。
- Git不会的童鞋网上搜一下，很简单，在此附上一张命令速查表，方便快捷，虽然貌似后面都没用到git命令。
![](http://upload-images.jianshu.io/upload_images/1741029-4f7a27cef1b46665.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![Git命令](http://upload-images.jianshu.io/upload_images/1741029-e5035d2c74ba60c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

<!--more-->

- 然后是**[Node.js](https://nodejs.org/en/)下载**,稳定版和最新版都可以，个人喜好，Hexo是靠node驱动，所以以上除了github以外都是是必须的。
- 有不想用国外网站的童鞋也可以使用国内的[Coding](https://coding.net)，原理一样，反正都是仿Github的。虽说国外网站有可能被墙，而且比起国内的慢一点，但是我同时部署了Coding和Github，后者瞬间就能访问了，而前者，呵呵。
- 对了，第一个坑来了，安装完毕之后一定要重启，不然后面使用*npm*命令安装Hexo时会提示找不到命令，然后才能继续后面的步骤。重启完毕之后就可以愉快地开始安装了，在Git Bash端执行下面的命令就可以安装Hexo了。

    	$ npm install hexo-cli -g

- 然后初始化hexo，进入文件之后就可以执行后面的命令了。
	    $ hexo init blog
	    $ cd blog
    
- 介绍一下hexo下用到的命令：
 
		$ hexo g/generate #生成静态文件
		$ hexo s/server #启动服务器，主要用来本地预览
		$ hexo d/deploy #将本地文件发布到github或Coding上
		$ hexo n/new "postName"#新建一篇文章
		$ hexo n/new page "pageName" #新建页面
		$ hexo h/help # 查看帮助
		$ hexo v/version #查看Hexo的版本

- 进入文件之后执行**hexo g**和**hexo s**之后然后用浏览器打开*http://localhost:4000/*，不出意外的话，你就可以看到你的博客了，就像下面这个样子的，当然这个博客只是在本地的，hexo3.0使用的默认主题是landscape。

![](http://upload-images.jianshu.io/upload_images/1741029-22f51ce15050f2af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 然后你就可以进行愉快的个性化玩耍了，其中也是各种坑，首先是挑选自己喜欢的[主题](https://github.com/hexojs/hexo/wiki/Themes)，然后使用**git clone**下载到本地，就可以开心地设置了。
- 首先打开hexo文件的_condig.yml配置文件，还有一个是themes主题下你选择主题的配置文件_condig.yml，两个配置文件名一样，但是一个是主配置文件，一个是主题配置文件，不要搞混。
- 配置过程中也有不少坑，有遇到一个地方多了或者少了个空格什么的导致整个项目不能运行的，还有配置之后没有达到预期效果的，其实只要按照你选择的主题的文档来，仔细一点，基本上是没什么问题的。
- 当然，我就是按照文档来配置的，中间也是各种各样的问题，但是，基本上都是网上查查就能查到的普通问题。
- 我选的主题是[Next.Picces](https://github.com/iissnan/hexo-theme-next),配置好之后效果如下所示

![](http://upload-images.jianshu.io/upload_images/1741029-80b1d4c4eb437f5d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 有点小清新是不是，嗯，[地址](https://halshaw.github.io/)在此,欢迎来踩。
- 主题中所包含的功能就已经很多了，比如百度统计、打赏、搜索等功能，只要去找到文件中相对应的地方，然后设置就可以使用了。愚蠢的我一开始还想去自己实现一些功能，但是后来仔细阅读主题文档之后才发现，原来都有啊，其自带的功能已足够满足我等骚年。
- 所以我又去百度统计、多说、leancloud网站注册之后，然后拿到id之后放在配置文件中相应的地方就可以了，而且还实现了打赏功能，但是并没什么卵用。
- 使用文档很重要啊，这是我主题的[使用文档](http://theme-next.iissnan.com/getting-started.html)。

![](http://upload-images.jianshu.io/upload_images/1741029-b94e68dd00f6f723.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 好了，到此，你已经配置好本地，然后预览好之后就可以发布了，使用下面

		hexo g
		hexo d

- 命令就可以啦，但是天有不测风云，遇到错误，

 		ERROR Deployer not found : github

- 不用急，执行一下这条命令就可以，还有可能遇到说你的github地址不是仓库，那是因为你的地址没有设置正确，检查一下空格之类的。

		npm install hexo-deployer-git --save

- 要想以上命令起作用，前提是你的配置文件已经配置如下，这个是同时发布到Github和Coding上面

		deploy:
		  type: git
		  repo: 
		    coding: git@git.coding.net:HalShaw/halshaw.git,master
		    github: https://github.com/HalShaw/halshaw.github.io.git,master


- 如果只想发布到GIthub上，稍微改一下

		deploy:  
		  type: git
		  repository: https://github.com/HalShaw/halshaw.github.io.git 
		  branch: master

- 一切顺利的话，就可以啦。但是，慢着，又出现问题了。命令行返回
		
		Invalid argument		

- what?什么鬼？明明本地都可以成功预览了，但是为什么发布不了了呢，我就被这个**hexo d**搞了好久，查了好久才明白，原因是我在我的hexo目录下，我又执行了

		npm install

- 又安装了一个hexo，所以不能发布了，所以删除之后就可以高兴滴执行**hexo d**啦。
- 谢天谢地，终于发布上去了，然而，不到一分钟，我就收到了Github发来的邮件

![](http://upload-images.jianshu.io/upload_images/1741029-fbd6b116e700b063.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

创建page失败，madan，招谁惹谁了我，怎么遇到这么多问题呢。又是一番搜索之后找到了一个我认为满意的答案

		- Make a backup of the content locally
		- Delete the repository from GitHub
		- Delete the repository locally
		- Recreate the repository, ensuring that you don't include the repository within the repository locally, which is what you did previously
		- Push it to GitHub

- 意思就是我git仓库里面又包括其他仓库啦，让我把他们搞好之后再上传，果然，我到主题文件下把.git文件删除之后，然后再次发布，终于，大功告成。就是刚才那个小清新的博客啦。
- 然后就开始写文章吧，该博客只支持[markdown](http://www.jianshu.com/p/q81RER/)语法，所以不会的小伙伴也不要怕，花个几分钟就可以上手啦。在Git终端输入上面的新建文章命令就可以新建一篇文章啦。然后会显示文章所在路径，找到并打开开始写文章吧。

	
		$ hexo n my first
		INFO  Created: D:\hexo1\hexo\source\_posts\first.md


- 打开之后是这样的，你可以自己设置标签和分类，然后就开始写你自己的故事吧。

		title: my first#标题和日期必须唯一，因为访问文章的url是根据二者生成的
		date: 2016-08-25 15:33:58
		tags: 'try' #标签
		categories: "技术" #分类


- 然后之后写文章每次部署都需要执行如下的命令，需要先清理数据库，生成静态文件之后预览，然后发布。


		hexo clean
		hexo g
		hexo s
		hexo d


- 好啦，到这里，文章已经写好，然后发布之后就可以去找小伙伴炫耀啦。

### 本文为作者原创，保留所有权，欢迎转载，转载请注明出处，标明作者。