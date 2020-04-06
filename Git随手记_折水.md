### 配置git与版本库



#### 安装git

```sudo apt-get install git```



#### 在本地配置用户名与email地址

```git config --global user.name "Your name" ```

```git config --global user.email "email@example.com"```

这样表示本台机器上所有git仓库均采用这个配置，```--global```为可修改项，可以对某些需要自定义的git仓库进行设置



#### 版本库、仓库-repository

创建一个空的仓库，并将其初始化使得可以受git管理

```mkdir my_repository ```

```cd my_repository```

```git init```

在仓库中添加第一个文件```redeme.txt```，并使用```git add```，```git commit -m```提交

```gedit redeme.txt```

```git add redeme.txt```

```git commit -m "wrote a redeme file"```

-每次修改文件后，都i需要先使用```git add```将修改内容推送至缓冲区，然后使用```git commit```提交

``git status``和``git diff``可以查看当前本地仓库缓存区是否有内容尚未提交，和较直观地显示未被提交部分具体改动了哪些部分



### 时光机穿梭

#### 版本回退

使用``git log  ``可以查看之前从近到远所有的更新日志

如果需要较为精简的版本，可以输入``git log --pretty=online``，会隐去时间与作者信息

输出的信息中，``HEAD``表示当前版本

``git reset --hard HEAD^                      #回退到上一个版本`` 

``git reset --hard <commit id>          #回退到<commit id>所在的版本``

git的版本回退速度很快，其原理仅是将指向``HEAD``的指针进行移动

当然若已经进行过版本回退，不知道之前最新版本的``commit id``，可是用``git reflog``查看之前的``commit id``



#### 工作区与暂存区

工作区：就是本地目录，上述提到的``my_repository``

版本库：工作区有一个目录``.git``，但它不算工作区，而是Git的版本库
                  版本库中很重要的两部分：暂存区(stage/index)和分支(master，暂时仅此一个分支)

![git-repo](https://www.liaoxuefeng.com/files/attachments/919020037470528/0)



前面的``git add``将工作区内容添加到暂存区

而``git commit``将暂存区内容提交到当前分支

简单理解为，需要提交的文件统统丢到暂存区，然后，一次性提交暂存区的所有修改

``git commit``只会将暂存区的内容提交，而不会提交工作区的改动，每次``git commit``前需要先将工作区的内容``git add``至暂存区。

git跟踪并管理的是修改，而非文件



#### 撤销修改

场景一：需要丢弃工作区的修改时

``git checkout -- readme.txt         # 只丢弃工作区内容，对暂存区与版本库没有任何影响``

场景二：需要丢弃暂存区修改时

``git reset HEAD readme.txt      #此处将暂存区的内容丢进了工作区，回退到了场景一``

场景三：需要修改已经提交到版本库的内容时，参考版本回退一节



#### 删除文件

1.举个栗子，在本地新建一个文档test.txt，并同步至版本库

``gedit test.txt  ``

``git add test.txt``

``git commit -m "add test.txt"``

在终端运行指令删除它

``rm test.txt      # 在工作区删除了文件``

此时可以选择``git checkout``撤销工作区的动作，将文件恢复

``git checkout -- test.txt       # 不要忘记  -- ``

也可以运行``git rm``将删除动作同步到暂存区

``git rm test.txt      # git add/rm 是对应的两种操作，后续需要进行git commit``

``git commit -m "remove test.txt"``



### 远程仓库

#### 添加远程库

​	创建SSH Key

​	``$ ssh-keygen -t rsa -C "youremail@example.com"     # 生成id_rsa与	id_rsa.pub两个文件``

​	将生成的公钥与github绑定

​	Account setting -> SSH and GPG Keys -> New SSH Key

​	填入任意title，在key文本框粘贴id_rsa.pub的内容

​	在github创建一个新的空仓库

​	Create a new repository   # 在这个栗子中，仓库名为First_test.git

​	将本地版本库同步到远程仓库

​	``git remote add origin https://github.com/Amnesiacers/First_test.git``

​	``git push -u origin master     # -u 表示将本地的master分支与远程的master分	支关联起来 ``

​	之后只要本地``master``分支的版本库有了修改，就可以通过下面的指令将	最新修改推送至github

​	``git push origin master    ``

​	注意，在``git push``之前，不要忘记先``git add``与``git commit``



#### 从远程库克隆

​	``git clone git@github.com:Amnesiacers/First_test.git``

​	``git clone https://github.com/Amnesiacers/First_test.git``

​	两种不同的协议，https与ssh协议

​	https兼容性更好，ssh更快



### 分支管理

#### 创建与合并分支

master、dev指向提交，而HEAD指向分支master、dev等

一开始的时候，`master`分支是一条线，Git用`master`指向最新的提交，再用`HEAD`指向`master`，就能确定当前分支，以及当前分支的提交点

![git-br-initial](https://www.liaoxuefeng.com/files/attachments/919022325462368/0)

每次提交，`master`分支都会向前移动一步，这样，随着不断提交，`master`分支的线也越来越长。

当我们创建新的分支，例如`dev`时，Git新建了一个指针叫`dev`，指向`master`相同的提交，再把`HEAD`指向`dev`，就表示当前分支在`dev`上

Git创建一个分支很快，因为除了增加一个`dev`指针，改改`HEAD`的指向，工作区的文件都没有任何变化！

不过，从现在开始，对工作区的修改和提交就是针对`dev`分支了，比如新提交一次后，`dev`指针往前移动一步，而`master`指针不变



查看分支:		``git brach ``

创建分支:		``git brach <name>``

删除分支:		``git brach -d <name>``

切换分支:		``git checkout <name>``或``git switch <name>``

创建+切换分支：	``git checkout -b <name>``或``git switch -c <name>``

合并分支到当前分支： ``git merge <name>				#git merge用于合并指定分支到当前分支``



#### 解决冲突

当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。

解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。

用`git log --graph`命令可以看到分支合并图。

注：

``git log --graph --prety=online      # 只显示版本号与每次的commit描述``

``git log --graph --prety=online --abbrev-commit      # 进一步只显示版本号前几位``



#### 分支管理

合并分支时，加上`--no-ff`参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而`fast forward`合并就看不出来曾经做过合并。

``git checkout -b dev    # 创建并切换到新分支dev``

``git add readme.txt      # 修改dev分支下的readme.txt``

``git commit -m "add merge"``

``git checkout master``

``git merge --no-ff -m "merge with no-ff"``





#### Bug分支

修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；

当手头工作没有完成时，先把工作现场`git stash`一下，然后去修复bug，修复后，再`git stash pop`，回到工作现场；

``git stash``

``git checkout master``

``git add && commit ...``

``git checkout dev``

``git stash pop    # 或者git stash apply  ， pop恢复工作区并删除之前的stash，apply只恢复工作区不删除``

``git stash list     # 可以用来查看之前保存过的stash``



另外，git提供了将某次``git commit``的修改“复制”到另一个分支的操作,但需要知道之前进行``git commit``那次操作时的``commit id``：``git cherry-pick``

``git cherry-pick <commit id> ``





#### feature分支

feature分支用来存放一些新功能

``git checkout -b feature-xx``

怎样丢弃一个还未被合并过的分支？

``git branch -D <branch name>``





#### 多人协作

##### 推送分支

要查看远程库的信息，用`git remote`,或者，用`git remote -v`显示更详细的信息

``git remote -v``

用`git push`将本地版本库提交到远程库

``git push github master``

``git push github dev``

但是，并不是一定要把本地分支往远程推送，那么，哪些分支需要推送，哪些不需要呢？

- `master`分支是主分支，因此要时刻与远程同步；
- `dev`分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；
- bug分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；
- feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。

#### 

#####  捉取分支

在``git push``时提示失败，显示远程分支比本地更新，要先用``git pull``捉取远程的新提交，并试图合并

在本地创建和远程分支对应的分支，使用`git checkout -b branch-name origin/branch-name`，本地和远程分支的名称最好一致

如果合并有冲突，则手动解决，并在本地``git add & commit ... ``，参考分支合并

没有冲突或者解决掉冲突后，再用`git push `推送就能成功！

如果`git pull`提示`no tracking information`，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream-to  origin/<branch-name> <branch-name> `

举个栗子``<branch-name> = dev``，就是建立本地``dev``与远程``origin/dev``之间的链接

