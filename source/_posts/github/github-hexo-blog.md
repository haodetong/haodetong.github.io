---
title: github+hexo博客并备份
date: 2017-08-14 10:34:20
tags: github
category: github
keywords: Hexo, github
description: github+hexo搭建静态博客以及git常用命令。

---

### 一、hexo

1、官方网址：[https://hexo.io/](https://hexo.io/)

2、常用命令
	
	npm install hexo -g #安装  
	npm update hexo -g #升级  
	hexo init #初始化
	
3、简写
	
	hexo n "我的博客" == hexo new "我的博客" #新建文章
	hexo p == hexo publish #草稿
	hexo g == hexo generate #生成
	hexo s == hexo server #启动服务预览
	hexo d == hexo deploy #部署
	
4、布署

修改_config.yml
	
	# Deployment
	## Docs: http://hexo.io/docs/deployment.html
	deploy:
  		type: git
  		repository: git@***.github.com:***/***.github.io.git
  		branch: master
	
### 二、Github

1、摘抄
	
[常用 Git 命令清单](http://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html)

[Git远程操作详解](http://www.ruanyifeng.com/blog/2014/06/git_remote.html)

![MacDown Screenshot](http://image.beekka.com/blog/2014/bg2014061202.jpg)

* Workspace：工作区
* Index / Stage：暂存区
* Repository：仓库区（或本地仓库）
* Remote：远程仓库

2、常用命令

a、新建本地仓库

	# 在当前目录新建一个Git代码库
	$ git init

	# 新建一个目录，将其初始化为Git代码库
	$ git init [project-name]

	# 下载一个项目和它的整个代码历史
	$ git clone [url]	

b、增加/删除文件
	
	# 添加指定文件到暂存区
	$ git add [file1] [file2] ...

	# 添加指定目录到暂存区，包括子目录
	$ git add [dir]

	# 添加当前目录的所有文件到暂存区
	$ git add .
		
	# 删除工作区文件，并且将这次删除放入暂存区
	$ git rm [file1] [file2] ...
	
	# 改名文件，并且将这个改名放入暂存区
	$ git mv [file-original] [file-renamed]
	
c、代码提交
	
	# 提交暂存区到仓库区
	$ git commit -m [message]
	
	# 提交暂存区的指定文件到仓库区
	$ git commit [file1] [file2] ... -m [message]
	
	# 提交时显示所有diff信息
	$ git commit -v

d、分支

	# 列出所有本地分支
	$ git branch
	
	# 列出所有远程分支
	$ git branch -r
	
	# 列出所有本地分支和远程分支
	$ git branch -a
	
	# 新建一个分支，但依然停留在当前分支
	$ git branch [branch-name]
	
	# 新建一个分支，并切换到该分支
	$ git checkout -b [branch]
		
	# 新建一个分支，与指定的远程分支建立追踪关系
	$ git branch --track [branch] [remote-branch]
	
	# 切换到指定分支，并更新工作区
	$ git checkout [branch-name]
	
	# 切换到上一个分支
	$ git checkout -
	
	# 建立追踪关系，在现有分支与指定的远程分支之间
	$ git branch --set-upstream [branch] [remote-branch]
	
	# 合并指定分支到当前分支
	$ git merge [branch]
	
	# 删除分支
	$ git branch -d [branch-name]
	
	# 删除远程分支
	$ git push origin --delete [branch-name]
	$ git branch -dr [remote/branch]	
	
e、查看信息
	
	# 显示有变更的文件
	$ git status
	
	# 显示当前分支的版本历史
	$ git log
	
	# 显示暂存区和工作区的差异
	$ git diff
	
f、远程同步

	# 下载远程仓库的所有变动
	$ git fetch [remote]
	
	# 显示所有远程仓库
	$ git remote -v
	
	# 显示某个远程仓库的信息
	$ git remote show [remote]
	
	# 上传本地指定分支到远程仓库
	$ git push [remote] [branch]
	
	# 强行推送当前分支到远程仓库，即使有冲突
	$ git push [remote] --force
	
	# 推送所有分支到远程仓库
	$ git push [remote] --all

g、撤销

	# 恢复暂存区的指定文件到工作区
	$ git checkout [file]
	
	# 恢复某个commit的指定文件到暂存区和工作区
	$ git checkout [commit] [file]
	
	# 恢复暂存区的所有文件到工作区
	$ git checkout .
	
	# 重置暂存区的指定文件，与上一次commit保持一致，但工作区不变
	$ git reset [file]
	
	# 重置暂存区与工作区，与上一次commit保持一致
	$ git reset --hard

3、Git远程操作详解

a、git clone

	#从远程主机克隆一个版本库
	$ git clone <版本库的网址>	

b、git remote
	
	#列出所有的主机
	$ git remote
	origin
	
	#查看远程主机的网址
	$ git remote -v
	origin  git@github.com:jquery/jquery.git (fetch)
	origin  git@github.com:jquery/jquery.git (push)
	
	#克隆版本库的时候，所使用的远程主机自动被Git命名为origin
	$ git remote show <主机名>
	
	#git remote add命令用于添加远程主机。
	$ git remote add <主机名> <网址>
	
	#git remote rm命令用于删除远程主机
	$ git remote rm <主机名>
	
	#git remote rename命令用于远程主机的改名。
	$ git remote rename <原主机名> <新主机名>
	
c、git fetch

git fetch 取回到本地的是远程主机版本库的更新

git fetch命令通常用来查看其他人的进程，因为它取回的代码对你本地的开发代码没有影响。

默认情况下，git fetch取回所有分支（branch）的更新。如果只想取回特定分支的更新，可以指定分支名。

	$ git fetch <远程主机名> <分支名>
	
比如，取回origin主机的master分支。

	$ git fetch origin master
	
d、git pull

git pull命令的作用是，取回远程主机某个分支的更新，再与本地的指定分支合并。它的完整格式稍稍有点复杂。

	$ git pull <远程主机名> <远程分支名>:<本地分支名>
	
比如，取回origin主机的next分支，与本地的master分支合并，需要写成下面这样

	$ git pull origin next:master
	
如果远程分支是与当前分支合并，则冒号后面的部分可以省略。

	$ git pull origin next
	
上面命令表示，取回origin/next分支，再与当前分支合并。实质上，这等同于先做git fetch，再做git merge。

$ git fetch origin
$ git merge origin/next

在某些场合，Git会自动在本地分支与远程分支之间，建立一种追踪关系（tracking）。比如，在git clone的时候，所有本地分支默认与远程主机的同名分支，建立追踪关系，也就是说，本地的master分支自动"追踪"origin/master分支。

Git也允许手动建立追踪关系。

	git branch --set-upstream master origin/next
	
上面命令指定master分支追踪origin/next分支。


e、git push

git push命令用于将本地分支的更新，推送到远程主机。它的格式与git pull命令相仿。

	$ git push <远程主机名> <本地分支名>:<远程分支名>

如果省略远程分支名，则表示将本地分支推送与之存在"追踪关系"的远程分支（通常两者同名），如果该远程分支不存在，则会被新建。

	$ git push origin master
	
上面命令表示，将本地的master分支推送到origin主机的master分支。如果后者不存在，则会被新建。

如果省略本地分支名，则表示删除指定的远程分支，因为这等同于推送一个空的本地分支到远程分支。
	
	$ git push origin :master
	# 等同于
	$ git push origin --delete master

上面命令表示删除origin主机的master分支。

三、hexo多个终端同步

1、在其中一个终端操作，push本地文件夹Hexo中的必要文件到yourname.github.io的hexo分支上
	
	//初始化本地仓库
	git init  
	
	//将本地与Github项目对接
	git remote add origin git@github.com:yourname/yourname.github.io.git
	
	//将必要的文件依次添加，有些文件夹如npm install产生的node_modules由于路径过长不好处理，所以这里没有用`git add .`命令了，而是依次添加必要文件，如下图所示
	git add source scaffolds themes .npmignore _config.yml package.json
	git commit -m "Blog Source Hexo"
	
	//新建hexo分支
	git branch hexo 
	
	//切换到hexo分支上
	git checkout hexo   
	
	//push到Github项目的hexo分支上
	git push origin hexo   
	
2、在另一终端完成clone和push更新
	
	//将Github中hexo分支clone到本地
	git clone -b hexo git@github.com:yourname/yourname.github.io.git  
	
	//切换到刚刚clone的文件夹内
	cd  yourname.github.io
	
	//注意，这里一定要切换到刚刚clone的文件夹内执行，安装必要的所需组件，不用再init  
	npm install    
	
	//新建一个.md文件，并编辑完成自己的博客内容
	hexo new post "new blog name"   
	
	//经测试每次只要更新source中的文件到Github中即可，因为只是新建了一篇新博客
	git add source 
	 
	git commit -m "XX"
	
	//更新分支
	git push origin hexo  
	
	//push更新完分支之后将自己写的博客对接到自己搭的博客网站上，同时同步了Github中的master
	hexo d -g

3、不同终端更新博客
	
	//先pull完成本地与远端的融合
	git pull origin hexo  
	
	hexo new post " new blog name"
	
	git add source
	
	git commit -m "XX"
	
	git push origin hexo
	
	hexo d -g