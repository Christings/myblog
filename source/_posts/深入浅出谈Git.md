---
title: 深入浅出谈Git
date: 2018-08-24 18:56:03
tags: git
category:
		- 工具篇
		- git
---
## 一.Git配置
	$ git config --global user.name "Your Name"
	$ git config --global user.email "email@example.com"
## 二.Git初始化
	$ git init
	$ git add .
	$ git commit -m "first commit"
	$ git push -u origin master
## 三.Git状态
	$ git status
	$ git diff readme.txt # 查看修改内容
## 四.Git时光机
### 1.版本回退
	$ git log # 查看最近到最远的提交日志
	$ git log --pretty=online # 格式更清晰
	
	# 在Git中，HEAD表示当前版本，上一个版本是HEAD^,上上一个版本是HEAD……^^，往上100个版本是HEAD~100
	
	$ git reset --hard HEAD^ # 回退到上一个版本
	
	$ git reset --hard 1094a # 1094a刚才的那个版本号，回退到未来的那个版本，前提是你的命令行窗口没有被关掉，版本号可以不写全
	
	# 如果回退到某个版本，关掉了电脑，第二天后悔了，想恢复到新版本怎么办？
	$ git relog # 记录你的每一次命令，此时可以找到你昨天的commit id是1094a
	
	小结：
	HEAD指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令git reset --hard commit_id。
	穿梭前，用git log可以查看提交历史，以便确定要回退到哪个版本。
	要重返未来，用git reflog查看命令历史，以便确定要回到未来的哪个版本。
### 2.工作区（Working Directory）和暂存区
	工作区（Working Directory）:learngit
	版本库（Repository）：隐藏目录.git
	Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支master，以及指向master的一个指针叫HEAD。

![enter description here](http://p2lakvkq0.bkt.clouddn.com/git1.png)

	把文件往Git版本库里添加的时候，是分两步执行的：
	第一步是用git add把文件添加进去，实际上就是把文件修改添加到暂存区；
	第二步是用git commit提交更改，实际上就是把暂存区的所有内容提交到当前分支。

	因为我们创建Git版本库时，Git自动为我们创建了唯一一个master分支，所以，现在，git commit就是往master分支上提交更改。

	你可以简单理解为，需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改。
![enter description here](http://p2lakvkq0.bkt.clouddn.com/git2.png)
![enter description here](http://p2lakvkq0.bkt.clouddn.com/git3.png)
### 3.管理修改
	为什么Git比其他版本控制系统设计得优秀，因为Git跟踪并管理的是修改，而非文件。
	$ git diff HEAD -- readme.txt # 查看工作区和版本库里最新版本区别
### 4.撤销修改
	$ git checkout -- readme.txt # 把文件在工作区的修改全部撤销掉
	两种情况：
	readme.txt自修改后还没有被放到暂存区，撤销修改就回到和版本库一模一样的状态；
	readme.txt已经添加到暂存区去，又做了修改，撤销修改就回到添加到暂存区后的状态；
	总之，就是让这个文件回到最近一次git commit或git add时的状态。
	# --很重要，如果没有，则变成了切换分支命令
	
	$ git add readme.txt # 已到暂存区，还未提交
	$ git reset HEAD readme.txt # 把暂存区修改撤销掉（unstage），重新放回工作区
	git reset命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用HEAD时，表示最新的版本。

	小结：
	场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令git checkout -- file。
	场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令git reset HEAD <file>，就回到了场景1，第二步按场景1操作。
	场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。
### 5.删除文件
	$ git add test.txt
	$ git commit -m "add"
	$ git rm test.txt # 从版本库中删除文件，再git commit提交
	$ git commit -m "delete"
	$ git checkout -- test.txt # 删除错了，从版本库恢复到误删的文件，恢复到最新版本
	git checkout其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。
	小结：
	命令git rm用于删除一个文件。如果一个文件已经被提交到版本库，那么你永远不用担心误删，但是要小心，你只能恢复文件到最新版本，你会丢失最近一次提交后你修改的内容。
## 五.远程仓库
	本地Git仓库和GitHub仓库之间的传输是通过SSH加密的，所以需要进行设置：
	1.创建SSH Key
	$ ssh-keygen -t rsa -C "youremail@example.com"
	$ cd ~/.ssh
	私钥：id_rsa
	共约：id_rsa.pub
	2.登录GitHub，将公钥放进配置文件
	为什么GitHub需要SSH Key呢？因为GitHub需要识别出你推送的提交确实是你推送的，而不是别人冒充的，而Git支持SSH协议，所以，GitHub只要知道了你的公钥，就可以确认只有你自己才能推送。

### 1.添加远程库
	在github上创建仓库
	$ git remote add origin git@github.com:Gladysgong/learngit.git # 本地仓库与远程库关联，远程库叫做origin
	$ git push -u origin master # 将本地库所有内容推送到远程库上
	把本地库的内容推送到远程，用git push命令，实际上是把当前分支master推送到远程。
	由于远程库是空的，我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。
	
	小结：
	要关联一个远程库，使用命令git remote add origin git@server-name:path/repo-name.git；
	关联后，使用命令git push -u origin master第一次推送master分支的所有内容；
	此后，每次本地提交后，只要有必要，就可以使用命令git push origin master推送最新修改；
### 2.克隆远程库
	$ git clone git@github.com:Gladysgong/gitskills.git
	Git支持多种协议，包括https，但通过ssh支持的原生git协议速度最快
## 六.分支管理
### 1.创建与合并分支
	每次提交，Git都把它们串成一条时间线，这条时间线就是一个分支。
	截止到目前，只有一条时间线，在Git里，这个分支叫主分支，即master分支。
	HEAD严格来说不是指向提交，而是指向master，master才是指向提交的，所以，HEAD指向的就是当前分支。

----------


	一开始的时候，master分支是一条线，Git用master指向最新的提交，再用HEAD指向master，就能确定当前分支，以及当前分支的提交点：
![enter description here](http://p2lakvkq0.bkt.clouddn.com/git4.png)

	每次提交，master分支都会向前移动一步，这样，随着你不断提交，master分支的线也越来越长


----------

	当我们创建新的分支，例如dev时，Git新建了一个指针叫dev，指向master相同的提交，再把HEAD指向dev，就表示当前分支在dev上：
![enter description here](http://p2lakvkq0.bkt.clouddn.com/git5.png)

	Git创建一个分支很快，因为除了增加一个dev指针，改改HEAD的指向，工作区的文件都没有任何变化！
	不过，从现在开始，对工作区的修改和提交就是针对dev分支了，比如新提交一次后，dev指针往前移动一步，而master指针不变：
![enter description here](http://p2lakvkq0.bkt.clouddn.com/git6.png)


	等我们在dev上的工作完成了，就可以把dev合并到master上。
	


----------

	Git怎么合并呢？最简单的方法，就是直接把master指向dev的当前提交，就完成了合并：
![enter description here](http://p2lakvkq0.bkt.clouddn.com/git7.png)

	所以Git合并分支也很快！就改改指针，工作区内容也不变！
	合并完分支后，甚至可以删除dev分支。删除dev分支就是把dev指针给删掉，删掉后，我们就剩下了一条master分支：
![enter description here](http://p2lakvkq0.bkt.clouddn.com/git8.png)

	实战：
	$ git checkou -b dev # Switched to a new branch 'dev',-b参数表示创建并切换
	$ git branch dev # 创建分支
	$ git checkout dev # 切换分支
	$ git branch # 查看当前分支
	$ git checkout master # 切换回master分支
	$ git merge dev # 将dev分支的工作成果合并到master分支
	# 注意Fast-forward信息，Git告诉我们，这次合并是“快进模式”，也就是直接把master指向dev的当前提交，所以合并速度非常快。
	$ git branch -d dev # 删除dev分支
### 2.解决冲突
	master分支和feature1分支各自分别都有新提交，变成了这样：
![enter description here](http://p2lakvkq0.bkt.clouddn.com/git9.png)
	
	此时，Git无法执行“快速合并”，只能试图把各自的修改合并起来，但这种合并就可能会有冲突。
	$ git merge feature1
	Auto-merging readme.txt # 合并失败
	CONFLICT (content): Merge conflict in readme.txt
	Automatic merge failed; fix conflicts and then commit the result.
	$ git status # 可告诉我们冲突的文件
	
	# 修改文件后再提交
	$ git add readme.txt 
	$ git commit -m "conflict fixed"
![enter description here](http://p2lakvkq0.bkt.clouddn.com/git10.png)
	
	$ git log --graph --pretty=online --abbrev-commit
	*   cf810e4 (HEAD -> master) conflict fixed
	|\  
	| * 14096d0 (feature1) AND simple
	* | 5dc6824 & simple
	|/  
	* b17d20e branch test
	* d46f35e (origin/master) remove test.txt
	* b84166e add test.txt
	* 519219b git tracks changes
	* e43a48b understand how stage works
	* 1094adb append GPL
	* e475afc add distributed
	* eaadf4e wrote a readme file
	
	$ git branch -d feature1
	
	小结：
	当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。
	解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。
	用git log --graph命令可以看到分支合并图。
### 3.分支管理策略
	合并分支时，如果Git会用Fast forward模式，但这种模式下，删除分支后，会丢掉分支信息。
	如果要强制禁用Fast forward模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。
	$ git merge --no-ff -m "merge with no-ff" dev # --no-ff参数表示禁用Fast forward
	# 因为本次合并要创建一个新的commit，所以加上-m参数，把commit描述写进去。
	$ git log --graph --pretty=oneline --abbrev-commit
	*   e1e9c68 (HEAD -> master) merge with no-ff
	|\  
	| * f52c633 (dev) add merge
	|/  
	*   cf810e4 conflict fixed
	...
![enter description here](http://p2lakvkq0.bkt.clouddn.com/git11.png)


----------

	master分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；
	那在哪干活呢？干活都在dev分支上，也就是说，dev分支是不稳定的，到某个时候，比如1.0版本发布时，再把dev分支合并到master上，在master分支发布1.0版本；
	每个人都在dev分支上干活，每个人都有自己的分支，时不时地往dev分支上合并就可以了。
	所以，团队合作的分支看起来就像这样：
![enter description here](http://p2lakvkq0.bkt.clouddn.com/git12.png)

	小结：
	合并分支时，加上--no-ff参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而fast forward合并就看不出来曾经做过合并。
### 4.bug分支

	每个bug都可以通过一个新的临时分支来修复，修复后，合并分支，然后将临时分支删除。
	$ git stash # 存储当前工作现场，等以后恢复工作继续工作
	$ git stash list # 查看存储的工作现场
	$ git stash apply # 恢复，但stash内容并不删除
	$ git stash drop # 删除stash内容
	$ git stash pop # 恢复，同时删除stash内容
	
	# 可以多次stash，然后用git stash list查看，再恢复指定的stash
	$ git stash apply stash@{0} # 恢复指定的stash

	小结
	修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；
	当手头工作没有完成时，先把工作现场git stash一下，然后去修复bug，修复后，再git stash pop，回到工作现场。
### 5.feature分支
	添加一个新功能时，你肯定不希望因为一些实验性质的代码，把主分支搞乱了，所以，每添加一个新功能，最好新建一个feature分支，在上面开发，完成后，合并，最后，删除该feature分支。

	一切顺利的话，feature分支和bug分支是类似的，合并，然后删除。
	就在此时，接到上级命令，因经费不足，新功能必须取消！
	$ git branch -d feature-vulcan # 销毁失败
	# Git友情提醒，feature-vulcan分支还没有被合并，如果删除，将丢失掉修改，如果要强行删除，需要使用大写的-D参数。
	$ git branch -D feature-vulcan # 强行删除
	# 如果要丢弃一个没有被合并过的分支，可以通过git branch -D <name>强行删除。
### 6.多人协作
	当你从远程仓库克隆时，实际上Git自动把本地的master分支和远程的master分支对应起来了，并且，远程仓库的默认名称是origin。
	$ git remote # 查看远程库信息
	$ git remote -v # 线上远程库更详细信息
	
	origin  git@github.com:michaelliao/learngit.git (fetch)
	origin  git@github.com:michaelliao/learngit.git (push)
	显示了可以抓取和推送的origin的地址。如果没有推送权限，就看不到push的地址。
#### 6.1 推送分支
	推送分支，就是把该分支上的所有本地提交推送到远程库。推送时，要指定本地分支，这样，Git就会把该分支推送到远程库对应的远程分支上：

	$ git push origin master
	$ git push origin dev # 推送dev分支
	但是，并不是一定要把本地分支往远程推送，那么，哪些分支需要推送，哪些不需要呢？

	master分支是主分支，因此要时刻与远程同步；
	dev分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；
	bug分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；
	feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。

	总之，就是在Git中，分支是否推送，视你的心情而定！
#### 6.2 抓取分支
	当其他人从远程库clone时，默认情况下，你的小伙伴只能看到本地的master分支。
	$ git branch 
	* master
	
	如果他要在dev分支上开发，就必须创建远程origin的dev分支到本地
	$ git checkout -b dev origin/dev # 创建本地dev
	$ git push origin dev 
	
	# 但是两人都对同一文件做了修改并推送，此时就会有冲突
	# Git已经提示我们，先用git pull把最新的提交从origin/dev抓下来，然后在本地合并，解决冲突，再推送
	$ git pull # 失败，原因是没有指定本地dev分支与远程origin/dev分支的链接，所以此时需设置dev和origin/dev的链接
	$ git branch --set-upstream-to=origin/dev dev # 设置origin/dev和dev的链接
	$ git pull # 成功
	# 这回git pull成功，但是合并有冲突，需要手动解决，解决的方法和分支管理中的解决冲突完全一样。解决后，提交，再push
	$ git push origin dev
#### 6.3 多人协作工作模式
	首先，可以试图用git push origin <branch-name>推送自己的修改；
	如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；
	如果合并有冲突，则解决冲突，并在本地提交；
	没有冲突或者解决掉冲突后，再用git push origin <branch-name>推送就能成功！
	如果git pull提示no tracking information，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream-to <branch-name> origin/<branch-name>
#### 6.4 小结
	查看远程库信息，使用git remote -v；
	本地新建的分支如果不推送到远程，对其他人就是不可见的；
	从本地推送分支，使用git push origin branch-name，如果推送失败，先用git pull抓取远程的新提交；
	在本地创建和远程分支对应的分支，使用git checkout -b branch-name origin/branch-name，本地和远程分支的名称最好一致；
	建立本地分支和远程分支的关联，使用git branch --set-upstream branch-name origin/branch-name；
	从远程抓取分支，使用git pull，如果有冲突，要先处理冲突。
### 7.Rebase(变基)
	多人在同一个分支上协作时，很容易出现冲突，冲突后再合并会使得分支很乱。
	$ git rebase
	rebase操作的特点：把分叉的提交历史“整理”成一条直线，看上去更直观。缺点是本地的分叉提交已经被修改过了。
	
	小结：
	rebase操作可以把本地未push的分叉提交历史整理成直线；
	rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。
## 七.标签管理
	发布一个版本时，我们通常先在版本库中打一个标签（tag），这样，就唯一确定了打标签时刻的版本。将来无论什么时候，取某个标签的版本，就是把那个打标签的时刻的历史版本取出来。所以，标签也是版本库的一个快照。
	Git的标签虽然是版本库的快照，但其实它就是指向某个commit的指针（跟分支很像对不对？但是分支可以移动，标签不能移动），所以，创建和删除标签都是瞬间完成的。
	所以，tag就是一个让人容易记住的有意义的名字，它跟某个commit绑在一起。
### 1.创建标签
	$ git tag v1.0 # 创建标签
	$ git tag # 查看所有标签
	# 默认标签是打在最新提交的commit上
	
	对过去的commit打标签
	$ git log --pretty=online --abbrev-commit
	$ git tag v0.9 f52c633
	
	# 标签不是按时间顺序列出，而是按字母排序
	$ git show v0.9 # 查看标签信息
	
	$ git tag -a v0.1 -m "version 0.1 released" 1094adb # -a指定标签名，-m指定说明文字
	注意：标签总是和某个commit挂钩。如果这个commit既出现在master分支，又出现在dev分支，那么在这两个分支上都可以看到这个标签
	小结：
	命令git tag <tagname>用于新建一个标签，默认为HEAD，也可以指定一个commit id；
	命令git tag -a <tagname> -m "blablabla..."可以指定标签信息；
	命令git tag可以查看所有标签。
### 2.操作标签
	$ git tag -d v0.1 # 删除标签
	因为创建的标签都只存储在本地，不会自动推送到远程。所以，打错的标签可以在本地安全删除。
	
	如果要推送某个标签到远程，使用命令git push origin <tagname>：
	$ git push origin v0.1 # 推送标签到远程
	$ git push origin --tags # 一次性推送全部未推送到远程的本地标签
	
	如果标签已推送到远程，删除远程标签麻烦一些，先删除本地，再删除远程
	$ git tag -d v0.9
	$ git push origin :refs/tags/v0.9 # 删除远程

	小结：
	命令git push origin <tagname>可以推送一个本地标签；
	命令git push origin --tags可以推送全部未推送过的本地标签；
	命令git tag -d <tagname>可以删除一个本地标签；
	命令git push origin :refs/tags/<tagname>可以删除一个远程标签。
## 八.GitHub Fork

![enter description here](http://p2lakvkq0.bkt.clouddn.com/git13.png)	
## 九.码云
	 $ git remote add github git@github.com:Gladysgong/learngit.git
	 $ git remote add gitee git@gitee.com:Gladysgong/learngit.git
	 $ git remote -v 
	 # 此时远程的名称不再叫origin，而是github和gitee
	 $ git push github master # 推送到github
	 $ git push gitee master # 推送到gitee
![enter description here](http://p2lakvkq0.bkt.clouddn.com/git14.png)

## 十.自定义Git
	$ git config --global color.ui true # 让Git显示颜色
### 1.忽略特殊文件
	在Git工作区根目录下创建一个特殊的.gitignore文件
	https://github.com/github/gitignore
	忽略文件原则：
	忽略操作系统自动生成的文件，比如缩略图等；
	忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的.class文件；
	忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件。
	
	.gitignore
	# Windows:
	Thumbs.db
	ehthumbs.db
	Desktop.ini

	# Python:
	*.py[cod]
	*.so
	*.egg
	*.egg-info
	dist
	build

	# My configurations:
	db.ini
	deploy_key_rsa
	
	有时候你想添加一个文件到Git，但发现添加不了，原因是这个文件被.gitignore忽略了
	ggit add -f app.class # 强制添加文件
	如果.gitignore写的有问题，需要找出哪个规则写错了
	$ git check-ignore -v App.class # 检查哪个规则有错
	
	小结：
	忽略某些文件时，需要编写.gitignore；
	gitignore文件本身要放到版本库里，并且可以对.gitignore做版本管理！
### 2.配置别名
	$ git config --global alias.st status # 为status设置别名st
	
	命令git reset HEAD file可以把暂存区的修改撤销掉（unstage），重新放回工作区。既然是一个unstage操作，就可以配置一个unstage别名：
	$ git config --global alias.unstage 'reset HEAD'
	
	配置一个git last，让其显示最后一次提交信息：
	$ git config --global alias.last 'log -l'
	
	git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
	
	配置文件：
	配置Git的时候，加上--global是针对当前用户起作用的，如果不加，那只针对当前的仓库起作用。
	$ cat .git/config # 仓库配置文件
	$ cat .gitconfig # 当前用户的Git配置文件
### 3.搭建Git服务器
	* 安装git
	$ sudo apt-get install git 
	* 创建一个git用户，用来运行git服务
	$ sudo adduser git
	* 创建证书登录
	收集所有需要登录的用户的公钥，就是他们自己的id_rsa.pub文件，把所有公钥导入到/home/git/.ssh/authorized_keys文件里，一行一个。
	* 初始化Git仓库
	$ sudo git init --bare sample.git
	$ sudo chown -R git:git sample.git
	* 禁用shell登录
	$ vi /etc/passwd
	git:x:1001:1001:,,,:/home/git:/bin/bash
	改为：
	git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell
	git用户可以通过ssh使用git，但无法登录git
	* 克隆远程仓库
	$ git clone git@server:/srv/sample.git
	
	管理公钥：
	Gitosis:https://github.com/res0nat0r/gitosis
	管理权限：
	Gitolite:https://github.com/sitaramc/gitolite
## 十一.总结
	Git Cheat Sheet --百度网盘pub
	Git官网：http://git-scm.com
	

	