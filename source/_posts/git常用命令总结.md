title: "git常用命令总结"
date: 2016/9/14 23:26:42  
updated: 2016/9/14 星期三 PM 1:51:54 
tags:
-  git
-  技术

categories: 技术辅助
---
##git 配置
* `git init [文件夹名]` 初始化一个git版本控制系统
* 查看所有配置信息 `git config --list`
* git帮助命令  `git help <verb> 或 git <verb> --help`
* 命令配置别名  `git config --global[或 --system] alias.别名 命令`，如`git config --global alias.ci commit`
* 打开配置文件： 
	* `git config -e` 打开版本库级别配置文件（位置在`.git/config`，要求在命令执行在版本库目录下）;  
	* `git config -e --global` 打开全局配置文件（位置在 `用户名/.gitcofnig`）; 
	* `git config -e --system` 打开系统配置文件  这三个配置文件的优先级别是从高到低

##git 查看

* `git diff` 
	* 不带任何选项参数选项和参数显示工作区与暂存区（提交暂存区，stage）中相比的差异
	* `git diff HEAD`  将工作区和HEAD（当前工作分支的版本库文件）相比
	* `git diff --cached(--staged)` 提交暂存区和版本库中文件的差异
* 查看本地文件状态 `git status -s` -s参数显示精简格式输出
* Git 查看对象内容 `git cat-file -p 哈希值`
* Git 提供了很多方法访问Git库中的对象（提交对象）
	1. 采用部分哈希值，只采用开头部分4位以上
    2. 使用master代表分支master中最新的提交，也可以使用全称`refs/heads/master`或`heads/master`
    3. 使用HEAD代表版本库中最近的一次提交
    4. 符号`^`可以用于指父提交
     	* `HEAD^` 代表版本库中上一次提交，最近提交的父提交
        * `HEAD^^`代表HEAD^的父提交
    5. 对于一个提交有多个父提交，可以在符号`^`后面用数字表示是第几个父提交
    6. 符号`~<n>`也可以用于指代祖先提交，如 `a5889~5`即相当于`a5889^^^^^`
    7. 提交对应的树对象，可以用类似如下语法访问`a5899^{tree}`
    8. 某一次提交对应的文件对象，可以用如下语法访问`a58999:path/to/file`
    9. 暂存区中的文件对象，可以用如下语法访问:`path/to/file`
>查看的文件状态包括*已跟踪*、*未跟踪*文件的修改状态，*暂存状态（add之后未提交）*
>>只要在Changes to be commited这行下面的文件是已暂存状态（即git add了些文件的修改或新add此文件作为追踪）
>
>>在Changes not staged for commit下面的，是已跟踪文件发生的变化，但还没有放到暂存区，要暂存这次更新，只要运行git add 此文件
*  版本表示法：`git rev-parse`
     
	 1. `git rev-parse --symbolic --branches` 显示分支
     2. `git rev-parse --symbolic --tags` 显示里程碑
     3. `git rev-parse --symbolic --glob=refs/*` 显示定义的所有引用
     4. 命令`git rev-parse`的另外一个重要功能就是将一个Git对象表达式表示为对应的SHA1哈希值。
              1. 显示 head的哈希值   `git rev-parse head`
              2. 命令git describe输出也可以解析为正确的sha1哈希值
              3. 可以同时显示多个表达式的sha1哈希值 ` git rev-parse master refs/heads/master`
              4. 可以用哈希值的前几位指代整个哈希值  `git rev-parse 6522 6522a0d`
              5. 里程碑的两种表示法均指向相同的对象  `git rev-parse A refs/tags/A`
              6. 显示里程碑A对应的目录树。`git rev-parse A^{tree}`  或`A:`
              7. 显示树里面的文件，两种方法：`git rev-parse A^{tree}:src/Makefile`  或`A:src/Makefile`
*  版本范围表示法：`git rev-list`   
      1. `git rev-list --oneline A` 版本A开始后的所有历史提交
* 查看文件具体更改内容 `git diff 文件名`  *查看尚未暂存的文件更新了那些内容*
* 浏览日志 `git log`
      1. 显示的日志范围  `git log` 后面可以接表示版本范围的参数，当不使用任何表示版本范围的参数时，相当于使用默认的参数HEAD，即显示当前head能够访问到的所有历史提交。  
      2. 分支图显示  通过`--graph`参数调用`git log` 可以显示字符界面的提交关系图，而且不同分支还可以用不同的颜色来表示。如果希望每次查看日志的时候都看到提交关系图，可以设置一个别名：`git config --global alias.glog "log --graph"`.  
      3. 显示最近的几条日志 可以使用参数-<n>显示最近的<n>条日志。如下面显示3条日志：`git log -3 --oneline`
      4. 显示每次提交的具体改动，使用参数-p可以在显示日志的时候同时显示改动。  `git log -p -1`
      5. 显示每次提交的变更概要。 使用-p参数会让日志输出显得非常冗余，当不需要知道具体的改动而只想知道改动在哪些文件上时，可以使用`--stat`参数。
      6. 如果只想查看和分析某一个提交，也可以使用`git show`或`git cat-file`命令
* 查看已经暂存的文件和上次提交时的差异 `git diff --cached 文件名`
* 查看提交日志  `git log --stat` *最近的显示在上面.加上stat可以显示每次提交的文件变更统计*
* `git log --graph --oneline -数字` 查看提交历史图 
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
* 用`reflog`挽救错误的重置 如果没有记下重置前master分支指向的提交id，想要重置回原来的提交就麻烦了。git通过`.git/logs`目录下日志文件记录了分支的变更。用`tail -5 .git/logs/refs/heads/master`可以查看。Git提供了一个命令`git reflog`命令对这个文件进行操作，使用`show`子命令可以显示此文件内容如：`git reflog show master | head -5` 这时只要使用`git reset --hard master@｛n｝`就可以重置到列出的生意引用了。

##git 操作
*  git重置  git重置命令有两种用法
   1. 用法一：`git reset [-q] [<commit>] [--] <paths>`
   2. 用法二：`git reset [--soft|--mixed|--hard|--merge|--keep] [-q] [<commit>]`
   	 	* 上面两种用法,其中`<commit>` 都是可选项，可以使用引用或提交ID,如果省略`<commit>`则相当于使用HEAD的指向作为提交ID
        * 两种用法的区别在于第一种用法在命令中包含路径`<paths>`,为了避免路径和引用（或者提交ID）同名而发生生冲突，可以在`<paths>`前面用两个连续的短线作为分隔
        * 第一种用法（包含了路径`<paths>`的用法），不会重置引用，更不会改变工作区，而是用指定提交状态（`<commit>`）下的文件(`<paths>`)替换掉暂存区中的文件。如命令`git reset HEAD <paths>` 相当于取消之前执行的`git add <paths>` 命令时改变的暂存区
        * 第二种用法（不使用路径`<paths>`的用法）则会重置引用。根据不同的选项，可以对暂存区或工作区进行重置。命令格式：`git reset [--soft | --mixed | --hard] [<commit>]`
              1. 使用参数 `--hard` 如 `git reset --hard <commit>`
                  * 替换引用的指向。引用指向指定的提交id
                  * 替换暂存区。替换后，暂存区内容和引用指向的目录树一致
                  * 替换工作区。替换后，工作区的内容变得和暂存区一致，也和HEAD所指向的目录树内容相同
              2. 使用参数`--soft` 如 `git reset --soft <commit>`
                  * 只会改变引用的指向，不改变暂存区和工作
              3. 使用参数`--mixed`或不使用参数（默认为`--mixed`） 如 `git reset --mixed <commit>`
                  * 会改变引用的指向和重置暂存区，但不改变工作区
  3. 一些常用的重置命令
          1. `git reset` 仅用head指向的目录树重置暂存区，工作区不会受到影响，相当于将之前的git add 命令更新到暂存区的内容撤出暂存区。引用了未改变，因为引用重置到head相当于没有重置
          2.  `git reset head` 同上
          3.  `git reset -- filename` 仅将文件filename的改变撤出暂存区，暂存区中其他文件不改变。相当于对命令`git add filename`的反向操作
          4. `git reset HEAD filename` 同上
          5. `git reset --soft HEAD^` 工作区和暂存区不改变，但是引用向前回退一次。当对最新提交的提交说明或提交的更改不满意时，撤销最新的提交以便重新提交。  之前介绍过一个修补提交命令 `git commit --amend`，用于对最新的提交进行重新提交以修补错误提交说明或错误的提交文件。修补命令实际上相当于执行了下面两条命令（文件`.git/COMMIT_EDITMSG`）保存了上次的提交日志。) 
	          1. `git reset --soft HEAD^`
	          2. `git commit -e -F .git/COMMIT_EDITMSG`
          6. `git reset head^` 工作区不改变，但是暂存区会回退到上一次提交之前，引用也会回退一次。
          7. `git reset --mixed head^` 同上
          8. `git reset --hard head^` 彻底撤销最近的提交。引用也会回到前一次，而且工作区和暂存区都会回退到上一次提交状态。自上一次以来的提交全部丢失。
*  git checkout 命令 Git是最常用的命令之一，同时也是一个危险的命令，因为这条命令会重写工作区。检出命令的用法用三：
      1. 用法一：`git checkout [-q] [<commit>] [--] <paths>`
      2. 用法二：`git checkout [<branch>]`
      3. 用法三：`git checkout [-m] [[-b] --orphan <new_branch>] [<start_point>]`
      	*	上面第一种用法和第二种用法的区别在于，第一种用法在命令中包含路径`<paths>`。为了避免路径和引用（或者提交ID）同名而发生冲突，可以`<paths>`前用两个连续的短线作为分隔。
      	*	第一种用法的`<commit>` 是可选项，如果省略则相当于从暂存区（index）进行检出。这和重置命令大不相同：重置的默认是值是HEAD，而检出的默认值是暂存区。因此重置一般用于暂存区重置（除非使用`--hard`参数，否则工作区不重置），而检出命令主要是覆盖工作区（如果`<commit>`不省略，也会替换暂存区中相应的文件）
      	* 第一种用法（包含了路径`<paths>`的用法）不会改变`HEAD`头指针，主要用于指定版本的文件覆盖工作区中对应的文件。如果省略<commit>，则会用暂存区的文件覆盖工作区的文件，否则用指定提交中的文件覆盖暂存区和工作区中对应的文件。
      	* 第二种用法（不使用路径`<paths>`的用法）则会改变HEAD头指针。之所以后面参数写作`<branch>`,是因为只有Head切换到一个分支才可以对提交进行跟踪，否则仍然会进入“分离头指针”的状态。在“分离头指针”状态下的提交不能被引用关联到，从而可能丢失。所以第二种用法最主要的作用是切换到分支。如果省略<branch>则相当于对工作区进行状态检查。
      	* 第三种用法主要是创建和切换到新的分支（`<new_branch>`）,新的分支从`<start_point>`指定的提交开始创建。新分支和我们熟悉的master分支没有什么实质的不同，都是在`refs/heads/`命令空间下的引用。
     4. 关于git checkout的常用命令
          * `git checkout branch` 检出branch分支。更新HEAD以指向branch分支，以及用branch指向的树更新暂存区和工作区
          * `git checkout` 汇总显示工作区，暂存区与HEAD的差异
          * `git checkout -- filename` 用暂存区中filename文件来覆盖工作区的filename文件。相当于取消自上次执行`git add filename`以来的本地修改
          *  `git checkout branch -- filename` 维护HEAD对象不变，用branch所指向的提交中的filename替换暂存区和工作区中相应的文件。注意会将暂存区和工作区中的filename文件直接覆盖。
          * `git checkout -- .`或`git checkout .`   注意用点号，会取消所有本地的修改（相对于暂存区）。相当于用暂存区的所有文件直接覆盖本地文件。
*  使用git stash可以用于保存和恢复工作进度，掌握这个命令对日常工作会有很大帮助。
      1. `git stash`  备份当前的工作区的内容，从最近的一次提交中读取相关内容，让工作区保证和上次提交的内容一致。同时，将当前的工作区内容保存到Git栈中。会分别对暂存区和工作区的状态进行保存
      2. `git stash list` 显示进度列表。此命令显然暗示了`git stash`可以多次保存工作进度，并且在恢复的进行选择。
      3. `git stash pop [--index] [<stash>]` 如果不使用任何参数，会恢复最新保存的工作进度，并将恢复的工作进度从存储的工作列表中清除。  选项`--index` 除了恢复工作区的文件外，还尝试恢复暂存区。
      4. 命令:`git stash [save [--patch]  [-k|--[no-] keep-index] [-q|--quiet] [<message>]]`  这条命令实际上是第一条`git stash`命令的完整版。如果需要在保存工作进度的时候使用指定的说明，必须使用如下格式：`git stash save "message....."`
          *  使用参数`--patch` 会显示工作区和HEAD的差异，通过对差异文件的编辑决定在进度中最终要保存的工作区的内容，通过编辑差异文件可以在进度中排除无关内容
          * 使用`-k`或`--keep-index` 参数，在保存进度后不会将暂存区重置。默认会将暂存区和工作区强制重置
      5. 命令：`git stash apply [--indxe] [<stash>] `除了不删除恢复的进度之外，其余和git stash pop命令一样
      6. 命令：`git stash drop [<stash>]` 删除一个存储的进度。默认删除最新的进度
      7. 命令：`git stash clear` 删除所有存储的进度。
      8. 命令：`git stash branch <branchname> <stash>` 基于进度创建分支。
*   变基操作：`git rebase --onto <newbase>  <since>  <till>`  变基操作如下：
      1. 首先会执行`git checkout` 切换到`<till>`. 因为会切换到`<till>`,因此如果`<till>`指向的不是一个分支，则变基操作是在`detached HEAD`(分离头指针)状态进行的，当变基操作结束后，还要对master分支重置以实现变基结果在分支中生效。
      2. 将`<since>..<till>` 所标识的提交范围写到一个临时文件中。`<since>..<till>`指包含`<till>`的所有历史提交排除`<since>`及`<since>`之前的历史提交后形成的版本范围。
      3. 将当前分支强制重置（`git reset --hard`）到`<newbase>`.相当于执行：`git reset --hard <newbase>`
      4. 从保存在临时文件中的提交列表中，将提交逐一按顺序重新提交到重置之后的分支上。
      5. 如果遇到提交已经在分支中包含，则跳过该提交
      6. 如果在提交过程中遇到冲突，则变基过程暂停。用户解决冲突后，执行`git rebase --continue`继续变基操作。或者执行`git rebase --skip`跳过此提交。或者执行`git rebase --abort`就此终止变基操作切换到变基前的分支上。
*  克隆操作 `git clone`的三种用法：
      1. 用法一：`git clone <repository>  <directory>`
      2. 用法二：`git clone --bare <repository>  <directory.git>`
      3. 用法三：`git clone --mirror <repository> <directory.git>`
          * 用法一将`<repository>` 指向的版本库创建一个克隆到`<directory>`目录。目录`<directory>`相当于克隆版本库的工作区，文件都会检出，版本库位于工作区下的`.git`目录中。
          * 用法2和用法3创建的克隆版本库都不包含工作区，直接就是版本库的内容，这样的版本库称为裸版本库。一般约定俗成裸版本库的目录名以`.git`为后缀，所以上面示例中将克隆了出来的裸版本库目录名写作`<directory.git>`
          * 用法3区别于用法2之处在于用法3克隆出来的裸版本对上游版本库进行了注册，这样可以在裸版本库中使用`git fetch`命令和上游版本库进行持续同步
*	合并操作 `git merge [选项] <commit>`   合并操作大多数情况下，只须提供一个`<commit>`（提交ID或对应的引用 `：分支`，里程碑等）作为参数。合并操作将`<commit>`对应的目录树和当前工作分支的目录树的内容进行合并，合并后的提交以当前分支的提交作为第一个父提交，以`<commit>`为第二个父提交。合并操作还支持将多个`<commit>`代表的分支和当前分支进行合并，过程类似。 默认情况下，合并后的结果会自动提交，但是如果提供`--no-commit`选项，则合并后的结果会放入暂存区，用户可以对合并结果进行检查，更改，然后手动提交。
* 增加文件到暂存区    `git add 文件名(-A所有文件变化)` 
*  `git add -i` 交互式选择性增加文件
*   文件追溯：`git blame`    Git的文件追溯命令可以指出是谁在什么时候，以及什么版本引入的此BUG。当针对文件执行git blame命令时，就会逐行显示文件，在每一行的行首显示此行最早是在什么版本引入的，由谁引入的
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
* `git checkout HEAD .`或`git checkout HEAD <file>`  命令会用HEAD指向的master分支中的全部或部分文件替换暂存区和工作区中的文件。这个命令是危险的，它不但会清除工作区中未提交的改动，也会清除暂存区中未提交的变动
* `git checkout . `或 `git checkout -- <file>` 命令，会用暂存区全部的文件或指定的文件替换工作区的文件。这个操作会清除工作区中未添加到暂存区中的改动
* 取消对文件的修改 `git checkout --文件` *相当于覆盖*
* `git clean -fd` 清除工作区中没有加入版本库的文件和目录（非跟踪文件和目录）.
*  git分支 
      1. 用法1：`git branch`
      2. 用法2: `git branch <branchname>`
      3. 用法3: `git branch <branchname>  <start-point>`
      4. 用法4：`git branch -d <branchname>`
      5. 用法5：`git branch -D <branchname>`
      6. 用法6：`git branch -m <oldbranch> <newbranch>`
      7. 用法7：`git branch -M <oldbranch> <newbranch>`
      8. 用法1用于显示本地分支列表。当前分支在输出中会显示为特别的颜色，并用星号“*”标识出来。用法2和用法3用于创建分支。用法2基于当前头指针（HEAD）指向的提交创建分支，新分支的分支名为`<branchname>`。用法3基于提交`<start-point>`创建新分支，新分支的分支名为`<branchname>`.  用法4和5用于删除分支。用法4在删除分支`<branchname>`时会检查所要删除的分支是否已经合并到其他分支中，否则拒绝删除。用法5会强制删除分支`<branchname>`，即使该分支没有合并到任何一个分支中。 用法6和用法7用于重命名分支。如果版本库中存在名为`<newbranch>`的分支，用法6拒绝执行重命名，而用法7会强制执行。
* git branch -r 显示所有远程分支


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
* Git里程碑  
    1. 显示里程碑  
          * `git tag -n<num>`   `-n`参数显示创建里程碑时的说明，`num`是限制显示的行数
          * 在查看日志时使用参数`--decorate`可以查看提交对应的里程碑及其他引用  `git log --oneline --decorate`
          * 使用命令`git describe`将提示显示为一个易记的名称。这个易记的名称来自于建立在该提交上的里程碑，若该提交没有里程碑则使用该提交历史版本上的里程碑并加上可理解寻址信息。
    2. 创建里程碑
          1. 用法一：`git tag             <tagname> [<commit>]`
          2. 用法二：`git tag -a          <tagname> [<commit>]`
          3. 用法三：`git tag -m <msg>    <tagname> [<commit>]`
          4. 用法四：`git tag -s 		 <tagname> [<commit>]`
          5. 用法五：`git tag -u <key-di>    <tagname> [<commit>]`
          6. 用法一是创建轻量级里程碑，用法2和3相同，都是创建带有说明的里程碑。用法3直接通过-m参数提供里程碑创建说明。 用法4和5相同，都是创建带GnuPG签名的里程碑，用法5用-u参数选择指定的私钥进行签名。创建里程碑需要输入里程碑的名称和一个可选的提交ID(`<commit>`),如果没有提供提交ID，则基于头指针HEAD创建里程碑。轻量级里程碑的创建过程没有记录，因此无法知道是谁创建的，何时创建的。在团队协同开发时不要用此种方式，使用后两种。
    3. 删除里程碑  `git tag -d 名字`
    4. 共享里程碑  创建的里程碑默认只在本地版本库中可见，不会因为对分支执行推送而将里程碑也推送到远程版本库。
          1. 显式推送以共享里程碑  `git push origin <tagname>`  
          2. 用户从版本库执行拉回操作时，会自动获取里程碑
          3. 如果共享库里的里程碑被更新，本地里程碑不会自动同步，必须采用显示的拉回操作，如：`git pull origin refs/tags/mytag2:refs/tags/mytag2`  代表用远程共享版本库的引用`refs/tag/mytag2`覆盖本地版本库的同名引用。这也就是如果本地已有同名的里程碑，默认不会从上游同步里程碑，即使两者里程碑的指向是不同的。这也就是里程碑一旦共享，就不要轻易再修改。
    5. 删除远程版本库的里程碑  使用命令`git push <remote_url>   :<tagname>`  该命令最后一个参数实际上是一个引用表达式，引用表达式一般的格式为`<ref>:<ref>`。该推送命令使用的引用表达式冒号前面的引用省略，其含义是将一个空值推送到远程版本库对应的引用中，亦即删除远程版本库中相关的引用。这个命令不但可以删除里程碑，还可以删除远程版本库中的分支。
