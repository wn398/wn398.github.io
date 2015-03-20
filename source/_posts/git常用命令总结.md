title: "git常用命令总结"
date: 2015-03-10 20:19:34 
updated: 2015/3/20 9:59:09 
tags:
-  git
-  技术

categories: git
---
##git 配置
* 查看配置信息 `git config --list`
* git帮助命令  `git help <verb> 或 git <verb> --help`

##git 操作
* 增加文件到暂存区    `git add 文件名(-A所有文件变化)` 
* 提交文件           `git commit -m "提交信息"`
* 跳过使用暂存直接提交 `git commit -a -m "提交信息"` (git会自动把所有已经跟踪文件暂存起来一并提交，从而跳过git add)
* 将add并commit的文件转化为未追踪状态 `git reset HEAD 文件名`
* 在本地创建一个分支 `git branch 分支名`
* 切换到本地另一个分支 `git checkout 要切换分支名`
* 本地新建并切换到新建的分支 `git checkout -b 分支名`
* 合并分支到当前分支 `git merge 要合并的分支名`
* 基于某一点创建分支 `git branch [start-point] 分支名` *开始点可以是分支名，提交id，tag，如果省略则使用当下的HEAD.*
* 删除本地分支 `git branch -D 分支名`
* 克隆远程版本库 `git clone (-n) 地址`(克隆远程代码到本地，默认为master,如果不想要文件可以加-n参数)
* 把本地代码push到远程具体分支 `git push (远程仓库名) (分支名)` 
	>如 git push origin master
	>>origin部分指定的是远程版本库git地址，master部分指定是push到哪一个分支上 
* 添加远程仓库（可以指定短名）`git remote add [shortname] [url]`
* 拉取远程仓库所有本地仓库没有的数据但不合并 `git fetch [remote-name]`
	>拉取下来的数据放在 （origin/对应远程分支名）下，你可以在本地访问该远程仓库中的所有分支，最后将某个分支合并到本地。fetch只拉取，并不会合并
* 拉取远程仓库数据并合并到当前分支 `git pull` 前提是设置了某个分支跟踪远程仓库某分支
* 推送某个分支到远程某个分支 `git push [远程库名] 本地分支名:远程分支名`
* 创建并切换一个本地分支去跟踪远程分支 `git checkout -b [本地分支名] [远程名/分支名]`  如果不想重命名本地分支现在可以用 `git checkout --track 远程名/分支名`
* 检出远程仓库一个文件 `git checkout 远程分支名（origin/master） 文件名`
* 删除远程分支  `git push 远程名 :远程分支` 这相当于命令`git push 远程名 本地分支：远程分支` 推送一个空的本地分支上去，相当于把远程分支给删除了
* 重命名本地的远程仓库名 `git remote rename 旧名 新名`
* 移除本地的远程仓库链接 `git remote rm`
* 从版本控制中移除文件并从硬盘上删除此文件 `git rm 文件名` *彻底删除*
* 仅从版本控制中移除文件，变成未跟踪文件 `git rm --cached 文件`
* 修改文件名 `git mv 旧文件名 新文件名`
* 撤消最后一次提交 `git commit --amend`
* 取消暂存 `git reset HEAD 文件`
* 取消对文件的修改 `git checkout --文件` *相当于覆盖*

##git 查看
* 查看本地文件状态 `git status`
>查看的文件状态包括*已跟踪*、*未跟踪*文件的修改状态，*暂存状态（add之后未提交）*
>>只要在Changes to be commited这行下面的文件是已暂存状态（即git add了些文件的修改或新add此文件作为追踪）
>
>>在Changes not staged for commit下面的，是已跟踪文件发生的变化，但还没有放到暂存区，要暂存这次更新，只要运行git add 此文件

* 查看文件具体更改内容 `git diff 文件名`  *查看尚未暂存的文件更新了那些内容*
* 查看已经暂存的文件和上次提交时的差异 `git diff --cached 文件名`
* 查看全部提交历史  `git log` *最近的显示在上面*
* 查看相应commit id的一条记录 `git id -1`
* 查看相应commit id的一条记录和相应修改内容 `git id -1 -p`
* 查看当前版本库所有分支(包括远程) `git branch -a` （带*的是当前所有分支）
* 查看所有本地分支 `git branch`
* 查看各个分支最后一个提交对象 `git branch -v`
* 查看哪些分支合并到当前分支 `git branch --merged`
* 查看尚未合并的工作 `git branch --no-merged`
* 查看当前本地配置有哪些远程仓库 `git remote (-v)`  
	>在克隆完某一个项目后，至少可以看到一个名为origin的远程库，Git默认使用这个名字来标识所克隆的原始仓库

* 查看远程仓库信息  `git remote show 远程库名` 
* 查看引用日志(Head和分支的引用) `git reflog`
* 查看具体某次的更改 `git show (id)` *不加id显示所有更改内容，加commt id显示具体一次的*

## Git 标签
>Git使用标签有两种类型，轻量级的（lightweight）和含附注的（annotated）.轻量级标签就像是个不会变化的分支，实际上它就是个指向特定提交对象的引用。而含附注的标签，实际上就是存储仓库中的一个独立对象，它有自身的校验和信息，包含着标签的名字，电子邮件地址和日期，以及标签说明，标签本身也允许使用GPG来签署或验证

* 列出所有标签 `git tag`
* 创建一个含附注的标签 `git tag -a 标签 -m 附注说明` *-a指定标签名字，-m指定标签说明，签署标签：如果你有自己的私钥，可以用GPG来签署标签，只需要把-a必为-s(git tab -s 标签名 -m "附注说明")*
* 创建轻量级标签 `git tag 标签名`
* 后期加注标签
	>用 git log --pretty=oneline 列出所有提交时的检验值
	只要在打标签时跟上对应提交对象的校验和（或前几位字符）即可如：`git tag -a v1.2 9fceb02`
* 分享标签
	>默认情况下，git push并不会把标签传送到远端服务器上，只有通过显式命令才能分享标签到远端仓库，其命令格式如同推送分支，运行`git push origin [tagname]`
	如果要一次推送所有本地新增的标签上去，可以使用[--tags]选项     `git push origin --tags`
