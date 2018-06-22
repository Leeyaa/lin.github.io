---
title: Jekyll到Hexo && 一些坑
date: 2018/6/21 10:00:00
updated: 2018/6/22 09:48:00
categories:
- Hexo
---

刚刚配好了Hexo，对比Jekyll来看，Hexo上next主题支持的更好，个人比较喜欢mist，所以不得不从Jekyll转到Hexo上来。但是hexo的备份明显不如Jekyll好做，git上master分支不存储博客源文件，备份源文件略显麻烦。网上资料比较杂乱，自己记录并整理一下搭建与备份过程，以备后用。

<!-- more -->

## Hexo+NexT+Github Page建站

其实Hexo和NexT官方文档上面步骤都是全的，但是对于初次接触Hexo的人来说过程步骤不太连贯，不太友好。本文章偷懒，并不打算记录Github部分，啥时候心情好可能会补上。

### 预备工作

- 一个Github Page仓库
- node.js
- git
- hexo-cli
- next（主题，美化，非必选）

### 博客搭建过程

1. ** node.js与git **都可以进行傻瓜式安装，注意安装的时候选择add path在系统中添加环境变量就可以。
2. ** 使用npm安装hexo **，官方推荐使用npm进行安装。npm是nodejs自带的包管理工具，方便对三方包进行管理和安装。npm可以在nodejs自带的Node.js commond prompt中直接运行，安装命令如下，
>     $ npm install -g hexo-cli

3. ** 建立本地博客 **
上传到github以前，先建立本地博客，方便测试。

	- 建一个文件夹存放你的博客，命名随意。比如我的为：
	>     E:\LIN\Workspace\gitpage

	- gitbash中进入所建目录，运行以下命令，将博客初始化完毕：

	>     $ hexo init #初始化hexo博客
	>     $ npm install #安装必要组件

	- 初始化完成后，系统会在博客中自动生成一篇名为hello world的文章，将初始化完的博客部署到本地，即可通过浏览器访问			localhost:4000来访问本地博客，部署命令：

	>     $ hexo generate
	>     $ hexo deploy

	- 致此，已经搭建完成一个本地Hexo博客。

4. ** 将本地博客部署到Github page **

	Hexo有两种方式访问Github Page,一种通过SSH，一种通过HTTPS。HTTPS方式配置较简单，但每次需要上传密码。SSH方式配置略微复杂，但是一次配置，终身受用。

	- 配置之前，需要先安装hexo-deployer-git 扩展组件，来支持hexo部署git。使用npm就可以，命令如下:
	
	>     npm install hexo-deployer-git --save
	
	- HTTPS方式
	
		使用Hexo配置git非常方便，只需要修改配置文件即可。我们所建博客目录下面，会存在两个_config.yml，一个存在于博客目录根目录，NexT官方文档称为** 站点配置文件 **，主要包含Hexo本身配置。另一个存在于themes主题中，称为** 主题配置文件 **，用来修改、美化主题。

        HTTPS方式访问git只要修改主题文件中deployment选项即可。

        >     # Deployment
		>     ## Docs: https://hexo.io/docs/deployment.html
        >     deploy:
        >       type: git
        >       repository: https://github.com/Leeyaa/Leeyaa.github.io
        >       branch: master # 此处指明静态博客分支，与后文中备份概念相关

        修改完成之后，可以运行以下命令来进行部署：

        >     hexo clean			#清除之前静态页面
		>     hexo generate			#生成静态页面
		>     hexo deploy(可缩写为d)  # 部署

	- SSH
	
	SSH方式略微复杂

		- 首先，我们在第一次安装git时，是有配置用户名和邮箱的，每次git上传或者clone的时候，git都会将该用户名与邮箱上传，用户名邮箱就保存在C:\Users\USERNAME或C:\Users\Administrator的.gitconfig文件中。如果是第一次配置git，请参考下面两条命令：
        >     $ git config --global user.name "Leeyaa"
		>     $ git config --global user.email Leeyaa@example.com
		
		git是一个蛮好用的东西，正在慢慢的边用边学。

        - 有了上述，我们就可以根据上面配置的用户名与邮箱生成SSH公钥私钥（RSA算法），我们将公钥发给GitHub的SSH管理员时，我们如果拿着私钥去访问GitHub，GitHub SSH管理员就可以进行公钥私钥配对，来完成认证。当然，第一次将公钥给GitHub时，GitHub管理员是需要我们输入密码来认证的。
        
        	- 确认配置过git用户名与邮箱之后，运行以下命令，生成公钥私钥，敲三次回车，位置与配置默认就可以：
        	>     ssh-keygen -t rsa -C "xxx@xxx.com" # 邮箱为GitHub邮箱
        	
        	公钥私钥位置在：
            >     C:\Users\LIN\.ssh
        - 生成公钥私钥后，将公钥复制，打开GitHub -> settings -> SSH and GPG keys -> New SSH key -> title随便起，将公钥复制到key框中 -> Add SSH key -> 输入GitHub密码 -> 成功。到这里，我们将公钥赋给了GitHub管理员。
        - 接下来，与HTTP方式相同，修改站点配置文件，如下：
        
        >     # Deployment
		>     ## Docs: https://hexo.io/docs/deployment.html
        >     deploy:
        >       type: git
        >       repository: git@github.com:Leeyaa/Leeyaa.github.io.git
        >       branch: master
        可以看到，只有repository一项不同，因为我们是通过ssh访问，所以需要库的ssh地址。SSH地址可以在GitHub -> GitHub page库 -> clone or download -> Use SSH获取

        - 之后步骤同HTTPS，将Hexo部署到GitHub我们就可以看到博客界面了。

### 新文章部署过程

上述过程已经搭建了一个完整的博客，只不过主题有些丑而已。之后，我们就可以方便的发布新文章了。
>     hexo clean
>     hexo generate
>     hexo deploy(可缩写为d)

Hexo提供给作者一键部署的功能，并给予了一定的debug信息，方便调试与部署，若站点配置文件或其他文件出现bug，部署时会返回不同的debug信息，需仔细观察。

### NexT主题




- Q1 怎么添加分类与标签
	1. 修改** 主题配置文件 **中menu选项，将需要的链接标签打开：
	
	>     menu:
	>     home: / || home
	>     categories: /categories/ || th
	>     archives: /archives/ || archive
	>     about: /about/ || user
	>     #tags: /tags/ || tags
	>     #schedule: /schedule/ || calendar
	>     #sitemap: /sitemap.xml || sitemap
  	>     #commonweal: /404/ || heartbeat
  	
	2. 上面只是在界面上生成了链接图标，但是并没有界面，生成界面运行如下：
  	
	>     hexo new page "categories"

	3. 生成页面之后，图标链接就可以打开了，但是打开后的网页没有内容，需要修改source/categories目录下的index.md文件,添加type行：
	
	>     ---
	>     title: categories
	>     date: 2018-06-21 10:04:52
	>     type: "categories"
	>     ---
	
	4. 这样，categories页面就完成了。
	
- Q2 侧边栏头像怎么显示？
	1. 将头像存放在\themes\next\source\images里面
	2. 修改主题配置文件avatar项，添加路径
	3. 注意，参数添加的路径中没有\themes\next\source\，因为发布到github上images是在根目录下。
	
- Q3 怎么更换主题模式
	Hexo自带主题比较丑，我也是奔着NexT.mist来的。Hexo更换主题非常方便，只需要将主题clone到blogpath/themes/下，然后修改站点配置文件中theme项即可。

	>     theme: next

	theme自带三种主题模式，可以在主题配置文件中修改scheme项：

	>     # ---------------------------------------------------------------
	>     # Scheme Settings
	>     # ---------------------------------------------------------------
	>     # Schemes
	>     #scheme: Muse
	>     scheme: Mist
	>     #scheme: Pisces
	>     #scheme: Gemini

	修改完后，重新部署博客即可。

## 备份博客源文件
Hexo与Jekyll不同，是将源文件生成网页后存放在GitHub上的，所以源文件不能通过GitHub来备份，如果换电脑，或者电脑系统崩溃，Hexo源文件就会丢失。常用的办法是在username.github.io库中新建一个分支来存储、备份源文件，换电脑以后，只要将源文件下载下来，再进行环境配置，便可使用。

1. 备份
	- 首先，在网页库user.github.io中**新建一个backup分支**.
	- ** 将上述backup分支设置为默认分支 ** *ps，其实静态网页发布的master分支对于用户来说是不可修改的，只需要我们在部署的时候指定了master分支，之后就完全不用手动去git操作master分支。而backup分支是需要我们经常来进行git操作的，所以这里将backup分支设为默认分支。*
	- **将backup分支clone到本地** ，我的文件夹名是Leeyaa.github.io。以后编辑博客，更改博客都会在这个分支文件夹中进行。这里有两种克隆指定分支的方法
		1. git clone -b backup https://github.com/Leeyaa/Leeyaa.github.io
		2. 通过Github客户端
	- **将除了.git文件夹以外的文件全都删除**
	- 将本地博客目录下的**_config.yml  themes/  source  scffolds/  package.json  .gitignore**几个文件与文件夹拷贝到backup分支目录。 * 我们实际需要备份的也就是这几个文件 *
	-** 将themes/next文件下的.git删除**，否则themes文件夹无法上传
	- 在分支文件夹中git bash执行以下命令**配置hexo git部署环境*** 这里可以检查分支是否为backup分支
		>     npm install hexo-deployer-git
		>     npm install
	- 在分支文件夹中使用git bash执行下面命令，**将backup分支的博客源文件部署到Github page上**。* 这样就完成了从本地到分支的备份迁移 *
		>     hexo generate
		>     hexo deploy
	- 将**分支文件夹提交到github**，完成备份过程。
		>     git add .
		>     git commit -m "注释信息"
		>     git push origin backup  #backup为分支名
		
2. 恢复
	在Github上已经备份了完整的博客源文件，想要在其他电脑上恢复，只需要将backup分支clone下来，再搭建hexo部署就可以了。下面给出恢复过程:
	- 将backup分支clone到本地
	- 安装hexo环境
		- node.js
		- git
	- 在分支文件夹中执行以下命令安装部署组件以及其他组件：
	>     npm install hexo-cli
	>     npm install hexo-deployer-git --save
	>     npm install

	这样，就完成了整个博客的恢复。


## 一些坑

1. 4000端口问题

 在建立本地博客过程中，若访问本地博客失败，可能是由于本地4000端口被占用，可通过以下命令来查询端口占用程序并杀死 
>     > netstat -nao|findstr "4000" #查询占用4000端口的程序，返回例如，'TCP 127.0.0.1:4000 0.0.0.0:0 LISTENING 9220'，其中9220为占用程序的PID
>     > tasklist |findstr"9220" #查询PID为9220的程序名称，后可通过任务管理器或命令行杀死占用端口的程序，杀死后，即可打开本地博客。

2. 使用haroopad的bug 

 使用haroopad修改md文章，若点击ctrl+s对文章进行保存，下一次打开文档，haroopad会自动在所有的末尾处添加换行符，是多添加一次！会严重干扰md格式。目前还未找到问题所在。
