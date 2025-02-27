# Git

Git是由Linus用C编写的分布式版本控制系统。分布式版本控制系统通常也有一台充当“中央服务器”的电脑，但这个服务器的作用仅仅是用来方便“交换”，每个人电脑里都有完整的版本库。



安装Git后设置用户名和邮箱地址

```cmd
$ git config --global user.name "sgKim"
$ git config --global user.email "sgkim925@gmail.com"
```



## 1. 版本库（Repository）

- `git init`

即仓库，可以理解为目录，里面的所有文件都能被Git管理，以便任何时刻都可以追踪历史。

因而，创建版本库时首先创建一个空目录。

```cmd
$ mkdir learngit
$ cd learngit
$ pwd
```

`pwd`命令——print work directory，以绝对路径显示当前工作目录

然后通过`git init`把目录变成Git可以管理的仓库。

```cmd
$ git init
Initialized empty Git repository in D:/AllProjects/learngit/.git/
```

当前目录下多了一个`.git`目录，此目录用于跟踪管理版本库。



## 2. 文件添加到版本库

- `git add`
- `git commit -m`

Git只能追踪文本文件的变动，如图片等二进制文件只能记录大小变化。**Word是二进制格式**，因此Git也无法追踪。

> Windows注意：**不要用记事本编辑代码**。微软在每个文件开头添加了0xefbbbf（十六进制）的字符，明明正确的程序一编译就报语法错误。

编写一个`readme.md`，放入learngit目录下。把一个文件放入Git只需两步

1. `Git add`把文件添加到仓库

```cmd
$ git add readme.md
```

2. `git commit`提交文件

```cmd
$ git commit -m "wrote a readme file."
[master (root-commit) 62dcf79] wrote a readme file.
 1 file changed, 1 insertion(+)
 create mode 100644 readme.md
```

`git commit`命令的`-m`参数是本次提交的说明。commit成功后会提示，添加了一行（`1 insertion(+)`）。

因为`commit`一次可以提交很多文件，因此可以多次`add`不同文件。

```cmd
$ git add file1.txt
$ git add file2.txt file3.txt
$ git commit -m "add 3 files."
```



## 3. 版本回溯

- `git status`
- `git diff`

### 3.1 修改文件

修改一下readme文件，使用`git status`查看文件状态。

```cmd
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   readme.md

no changes added to commit (use "git add" and/or "git commit -a")
```

`git status`命令可以让我们时刻掌握仓库当前的状态，上面的命令输出告诉我们，`readme.md`被修改过了，但还没有准备提交的修改。

使用`git diff`查看具体修改了哪些地方

```cmd
$ git diff readme.md
diff --git a/readme.md b/readme.md
index 7ca920b..5fa1713 100644
--- a/readme.md
+++ b/readme.md
@@ -2,4 +2,8 @@ This is a readme file written for learngit.

-Add A New Line。
\ No newline at end of file
+Add A New Line。
+
+
+
+Add啊啊啊。
\ No newline at end of file
```

提交修改和提交新文件是一样的两步，`git add`和`git commit`。

### 3.2 文件回溯

- `git log`
- `git reflog`
- `git reset`

每用一段时间就用`git commit`进行一次存盘，以便以后回溯。使用`git log`查看历史记录。

```cmd
$ git log
commit 78caed8fa113a62cf6fe057329f2154da911876d (HEAD -> master)
Author: sgKim <sgKim925@gmail.com>
Date:   Thu Jul 25 10:20:58 2024 +0800

    add a line again

commit 9995c27922706bf5a02cd84aaf36b386b130b2b2
Author: sgKim <sgKim925@gmail.com>
Date:   Thu Jul 25 10:12:51 2024 +0800

    add two lines for readme

commit 62dcf79f69d022e879a0d93f4edfbf6418ae86ce
Author: sgKim <sgKim925@gmail.com>
Date:   Thu Jul 25 10:08:07 2024 +0800

    wrote a readme file.
```

`git log`命令显示从最近到最远的提交日志，如果嫌输出信息太多，可以加上`--pretty=oneline`参数：

```cmd
$ git log --pretty=oneline
78caed8fa113a62cf6fe057329f2154da911876d (HEAD -> master) add a line again
9995c27922706bf5a02cd84aaf36b386b130b2b2 add two lines for readme
62dcf79f69d022e879a0d93f4edfbf6418ae86ce wrote a readme file.
```

一大串的文本`78caed...`是`commit id`，因为Git是分布式的版本控制系统，因此不能采用`1, 2, 3...`。

当进行版本回退时，**必须知道当前版本**是哪个版本。在Git中，用`HEAD`表示当前版本，也就是最新的提交`78caed8fa113...`。上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，当然往上100个版本写100个`^`比较容易数不过来，所以写成`HEAD~100`。

现在要把当前版本`add a line again`回退到`wrote a readme file`，使用`git reset`命令。

```cmd
$ git reset --hard 62dc
HEAD is now at 62dcf79 wrote a readme file.
```

`--hard`会回退到上个版本的已提交状态，而`--soft`会回退到上个版本的未提交状态，`--mixed`会回退到上个版本已添加但未提交的状态。

**版本号没必要写全**，前几位就可以了，Git会自动去找。

此时再查看`git log`

```cmd
$ git log
commit 62dcf79f69d022e879a0d93f4edfbf6418ae86ce (HEAD -> master)
Author: sgKim <sgKim925@gmail.com>
Date:   Thu Jul 25 10:08:07 2024 +0800

    wrote a readme file.
```

发现之前修改的两个版本都不见了。此时可以使用`git reflog`查看每一次命令。

```cmd
$ git reflog
62dcf79 (HEAD -> master) HEAD@{0}: reset: moving to 62dc
78caed8 HEAD@{1}: commit: add a line again
9995c27 HEAD@{2}: commit: add two lines for readme
62dcf79 (HEAD -> master) HEAD@{3}: commit (initial): wrote a readme file.
```

可以找到消失的两个版本。

Git的版本回退速度非常快，因为Git在内部有个指向当前版本的`HEAD`指针，当你回退版本的时候，Git仅仅是把HEAD从指向当前版本改为指向目标版本。然后顺便把工作区的文件更新了。所以你让`HEAD`指向哪个版本号，你就把当前版本定位在哪。

### 3.3 工作区和暂存区

工作区（Working Directory）指电脑里能看到的目录，比如`learngit`就是一个工作区。

工作区中的隐藏目录`/.git`不算工作区，而是Git的版本库（Repository）。Git的版本库里存了很多东西，其中最重要的就是称为**stage**（或者叫**index**）的暂存区，还有Git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`。

![image-20240725104035987](assets\工作区.png)

第一步是用`git add`把文件添加进去，实际上就是把文件修改添加到暂存区；

第二步是用`git commit`提交更改，实际上就是把暂存区的所有内容提交到当前分支。

创建Git版本库时，Git自动为我们创建了唯一一个`master`分支，所以，现在，`git commit`就是往`master`分支上提交更改。



新建一个文件LICENSE。使用`git status`查看。

```cmd
$ git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        LICENSE.txt

nothing added to commit but untracked files present (use "git add" to track)
```

此时的`LICENSE`处于UNTRACKED状态，使用`git add`后，再查看status。

```cmd
$ git add LICENSE
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   LICENSE.txt
```

此时暂存区为

![image-20240725104723776](assets\暂存区.png)

`git add`命令实际上就是把要提交的所有修改放到暂存区（Stage），然后，执行`git commit`就可以一次性把暂存区的所有修改提交到分支。

```cmd
$ git commit -m "add LICENSE, delete LICENSE.txt"
[master 0ad5b94] add LICENSE, delete LICENSE.txt
 1 file changed, 1 insertion(+)
 create mode 100644 LICENSE
```

一旦提交后，如果你又没有对工作区做任何修改，那么工作区就是“干净”的：

```cmd
$ git status
On branch master
nothing to commit, working tree clean
```

### 3.4 管理修改

Git由于其他版本控制系统的点在于，Git跟踪并管理的是修改而非文件。

如：第一次修改 -> `git add` -> 第二次修改 -> `git commit`，那么第二次修改不会生效，且`git diff`会显示工作区和当前版本库存在差异。



### 3.5 撤销修改

- `git checkout -- readme.md`
- `git reset HEAD readme.md`

`git checkout`丢弃工作区的修改。此时有两种情况

一种是`readme.md`自修改后还没有被放到暂存区，现在，撤销修改就**回到和版本库**一模一样的状态；

一种是`readme.md`已经添加到暂存区后，又作了修改，现在，撤销修改就**回到添加到暂存区**后的状态。

**总之，就是让这个文件回到最近一次`git commit`或`git add`时的状态**。

`git checkout -- file`命令中的`--`很重要，没有`--`，就变成了“切换到另一个分支”的命令，我们在后面的分支管理中会再次遇到`git checkout`命令。

如果不幸已经`git add`到了版本库中，但还没有commit，可以使用`git reset HEAD readme.md`把暂存区的修改撤销掉(unstage)，重新放回工作区。

> 场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。
>
> 场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`，就回到了场景1，第二步按场景1操作。
>
> 场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退，不过前提是没有推送到远程库。



### 3.6 删除文件

- `git rm`
- `git checkout -- file`

在工作区中删除已经commit的文件，使用`git status`，git会查出哪些文件被删除了：

```cmd
$ git status
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	deleted:    test.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

如果确实想删除，则使用`git rm`删掉以后commit

```cmd
$ git rm test.txt
rm 'test.txt'

$ git commit -m "remove test.txt"
[master d46f35e] remove test.txt
 1 file changed, 1 deletion(-)
 delete mode 100644 test.txt
```

如果删错了，则撤销修改，使用`git checkout -- test.txt`

```cmd
$ git checkout -- test.txt
```

`git checkout`其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。

如果一个文件已经被提交到版本库，那么你永远不用担心误删。



## 4. 远程仓库

### 4.1 连上GitHub

由于你的本地Git仓库和GitHub仓库之间的传输是通过SSH加密的，所以，需要一点设置：

1. 创建SSH Key

在用户主目录下，看看有没有.ssh目录，如果有，再看看这个目录下有没有`id_rsa`和`id_rsa.pub`这两个文件，如果已经有了，可直接跳到下一步。如果没有，打开Shell（Windows下**打开Git Bash**），创建SSH Key：

```
$ ssh-keygen -t rsa -C "sgkim925@gmail.com"
```

然后一路回车，使用默认值即可.

![image-20240725110640733](assets\私钥.png)

`id_rsa`是私钥，不能泄露出去，`id_rsa.pub`是公钥，可以放心地告诉任何人。

2. 登录GitHub，打开“Account settings”，“SSH Keys”页面

然后，点“Add SSH Key”，填上任意Title，在Key文本框里粘贴`id_rsa.pub`文件的内容。添加后可以看到ssh。

![image-20240725111307552](C:\Users\Kim\AppData\Roaming\Typora\typora-user-images\image-20240725111307552.png)

GitHub需要识别出你推送的提交确实是你推送的，而不是别人冒充的，所以，GitHub只要知道了你的公钥，就可以确认只有你自己才能推送。GitHub允许你添加多个Key。假定你有若干电脑，你一会儿在公司提交，一会儿在家里提交，只要把每台电脑的Key都添加到GitHub，就可以在每台电脑上往GitHub推送了。

在GitHub上免费托管的Git仓库，任何人都可以看到。



### 4.2 添加远程库

- `git remote add origin https://github.com/KimSeongGun/learngit.git`

- `git push -u origin master`
- `git push origin master`

在本地创建了一个库，在GitHub上也创建一个Git仓库，并让这两个仓库进行远程同步，GitHub上的仓库既可以作为备份，又可以让其他人通过该仓库来协作。



首先，登陆GitHub，然后，在右上角找到“Create a new repo”按钮，创建一个新的仓库：

![image-20240725111951865](assets\QuickStart.png)

GitHub提示：可以从这个仓库克隆出新的仓库，也可以把一个已有的本地仓库与之关联，然后，把本地仓库的内容推送到GitHub仓库。

**现在从本地Push到GitHub中**：在本地的Git Bash中运行（在learngit文件夹中）

```cmd
Kim@DESKTOP-MQHB6UF MINGW64 /d/AllProjects/learngit (master)
$ git remote add origin https://github.com/KimSeongGun/learngit.git
```

添加后，**远程库的名字**就是`origin`，这是Git默认的叫法，也可以改成别的，但是`origin`这个名字一看就知道是远程库。

下一步，就可以把本地库的所有内容推送到远程库上：

```cmd
$ git push -u origin master

Enumerating objects: 6, done.
Counting objects: 100% (6/6), done.
Delta compression using up to 32 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (6/6), 528 bytes | 528.00 KiB/s, done.
Total 6 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
To https://github.com/KimSeongGun/learngit.git
 * [new branch]      master -> master
branch 'master' set up to track 'origin/master'.
```

把本地库的内容推送到远程，用`git push`命令，实际上是把当前分支`master`推送到远程，由于远程库是空的，我们第一次推送`master`分支时，加上了`-u`参数。

Git会把本地和远程的master分支**关联起来**，在以后的推送或者拉取时就可以简化命令。

![image-20240725112742945](assets\remote.png)

可以看到，远程和本地已经一模一样了。

从现在起，只要本地做了提交，只需要

```cmd
$ git push origin master
```



### 4.3 删除远程库

- `git remote -v`
- `git remote rm origin`

使用`git remote -v`查看远程库信息

```cmd
$ git remote -v
origin  https://github.com/KimSeongGun/learngit.git (fetch)
origin  https://github.com/KimSeongGun/learngit.git (push)
```

然后根据名字删除，如

```cmd
$ git remote rm origin
```

此处的“删除”其实是解除了本地和远程的绑定关系，并不是物理上删除了远程库。



### 4.4 从远程库克隆

- `git clone`

先创建远程库，然后，从远程库克隆。

首先，登陆GitHub，创建一个新的仓库，名字叫`gitskills`，勾选`Initialize this repository with a README`，这样GitHub会自动为我们创建一个`README.md`文件。创建完毕后，可以看到`README.md`文件。

![image-20240725113344209](assets\clone.png)

使用`git clone`克隆一个本地库，注意git bash的pwd。

```cmd
$ git clone git@github.com:KimSeongGun/gitskills.git
```

Git支持多种协议，包括`https`，但`ssh`协议速度最快。



## 5. 分支管理

创建一个分支，在分支上干活，最后在合并到原来分支上，既安全又不影响他人工作。

### 5.1 创建和合并分支

- `git branch`
- `git checkout`
- `git merge`
- `git switch`

每次提交，Git都将他们串成一条时间线，这条时间线就是一个分支。`HEAD`指向`master`，`master`指向当前提交。**`HEAD`指向当前分支。**

<img src="assets\head.png" alt="image-20240727135746579" style="zoom:50%;" />

当创建一个新的分支`dev`后，令`HEAD`指向`dev`，即更改当前分支为`dev`分支。从此，每新提交一次，`dev`指针就向前移动一步而`master`指针不变。

<img src="assets\dev.png" alt="image-20240727140320899" style="zoom:50%;" />

假如在`dev`上的工作完成了，则把`dev`的指针合并到`master`上。

Git通过把`master`指向`dev`的当前提交完成合并。

<img src="assets\merge.png" alt="image-20240727140414674" style="zoom:50%;" />

完成合并后可以删除`dev`分支。

<img src="assets\rm.png" alt="image-20240727140449234" style="zoom:50%;" />

#### 1.首先创建`dev`分支

```cmd
$ git checkout -b dev
Switched to a new branch 'dev'
```

`-b`属性表示创建并切换分支。等价于两条命令：

```cmd
$ git branch dev
$ git checkout dev
```

然后查看当前分支，`git branch`命令会列出所有分支，当前分支前面会标一个`*`号。

```cmd
$ git branch
* dev
  master
```

#### 2.在dev上修改，并切换回master分支

```cmd
$ git add Notes.md
$ git commit -m "test"
```

假设`dev`工作完成，切换回`master`分支。

```cmd
$ git checkout master
```

可以发现在`dev`中修改的部分`master`中不见了。

#### 3.合并分支

把`dev`中的工作合并到`master`中

```cmd
$ git merge dev
Updating d46f35e..b17d20e
Fast-forward
 Notes.txt | 1 +
 1 file changed, 1 insertion(+)
```

合并完成后，就可以放心地删除`dev`分支了

```cmd
$ git branch -d dev
```

#### 4. switch

撤销修改是`git checkout -- <file>`，为防止混淆Git提供了新的命令专门用于切换分支。

```cmd
$ git swtich dev
```



### 5.2 解决冲突

创建一个新的分支`test`，修改Notes.md并提交。然后转回到`master`分支，也修改Notes.md并提交，即出现这样的情况。

<img src="assets\conflict.png" alt="image-20240727155448099" style="zoom:50%;" />

此时合并会出现冲突，Git无法执行“快速合并”，只能试图把各自的修改合并起来，但这种合并就可能会有冲突

```cmd
$ git merge test
Auto-merging Notes.md
CONFLICT (content): Merge conflict in Notes.md
Automatic merge failed; fix conflicts and then commit the result.
```

`git status`也能提示冲突内容

```cmd
$ git status
On branch master
Your branch is ahead of 'origin/master' by 4 commits.
  (use "git push" to publish your local commits)

You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)
        both modified:   Notes.md

no changes added to commit (use "git add" and/or "git commit -a")
```

Git用`<<<<<<<`，`=======`，`>>>>>>>`标记出不同分支的内容。只能手动修改并合并分支。

在有冲突的文件上进行手动修改，修改完成后添加并提交。注意此时不用再切换分支，因为强行合并后`master`和`test`两个分支的内容变得一样了。

```cmd
$ git add Notes.md
$ git commit -m "conflict fixed"
[master d18ea67] conflict fixed
```

可以使用带参数的`git log`查看分支的合并情况,，`git log --graph`命令可以看到分支合并图。

```cmd
$ git log --graph --pretty=oneline --abbrev-commit
*   d18ea67 (HEAD -> master) conflict fixed
|\
| * a772d01 (test) test in test
* | e84d9f7 test for master
|/
* cf686dc modify Notes 0727-2
* a119cab branch test
* 1d459b2 modified Notes 0727-1
* b350495 (origin/master) created Notes
```

最后删除`test`分支

```cmd
$ git branch -d test
```

### 5.3 分支管理策略

如果可能，Git会用`Fast forward`模式，但这种模式下，删除分支后，会丢掉分支信息（**`dev`指针还在，但是工作区被`master`覆盖**，见5-1）。

如果要强制禁用`Fast forward`模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。

因此在合并时，要在`master`工作区添加`--no-ff`属性，保留`dev`属性的工作区。

```cmd
$ git merge --no-ff -m "merge with no-ff" dev
```

此时合并后的结果为

<img src="assets\no-ff.png" alt="image-20240727161910677" style="zoom:50%;" />

`master`分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；干活应该在`dev`分支上，同时不同的人应该有自己的分支，并向`dev`分支上合并。等发布新版本时再从`dev`向`master`合并。

![image-20240727162058676](assets\mode.png)

合并分支时，加上`--no-ff`参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而`fast forward`合并就看不出来曾经做过合并。

### 5.4 Bug分支

- `git stash`
- `git stash list`
- `git stash pop`
- `git stash apply stash@{0}`
- `git stash drop`
- `git cherry-pick`

每个Bug都可以通过一个新的临时分支来修复，修复后合并分支再将临时分支删除。

假设现在要修复代码为101的Bug，自然需要创建`issue-101`进行修复，但是此时`dev`上的工作还没有完成无法提交。此时查看状态发现有任务没提交。

```cmd
$ git status
On branch master
Your branch is ahead of 'origin/master' by 9 commits.
  (use "git push" to publish your local commits)

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   Notes.md

no changes added to commit (use "git add" and/or "git commit -a")
```

因此需要`git stash`存储工作现场，等恢复现场以后继续工作。

```cmd
$ git stash
Saved working directory and index state WIP on master: 58f9467 开始写Bug分支，新建test.txt
```

此时再查看状态，发现工作区变干净了，此时可以放心地创建分支来修复bug。

```cmd
$ git status
On branch master
Your branch is ahead of 'origin/master' by 9 commits.
  (use "git push" to publish your local commits)

nothing to commit, working tree clean
```

首先确定要在哪个分支上修复bug，假定需要在`master`分支上修复，就从`master`创建临时分支：

```cmd
$ git checkout master
$ git checkout -b issue-101
Switched to a new branch 'issue-101'
```

现在修复bug，需要把“This is file”改为“This is a file”，然后提交：

```cmd
$ git add test.txt
$ git commit -m "修Bug"
[issue-101 2f0b6e2] 修Bug
 1 file changed, 1 insertion(+), 1 deletion(-)
```

修复完成后，切换到`master`分支，并完成合并，最后删除`issue-101`分支。

```cmd
$ git merge issue-101
Updating 8b4974a..2f0b6e2
Fast-forward
 test.txt | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
```

使用`git stash list`查看工作现场列表，使用`git stash apply`或`git stash apply stash@{0}`恢复。用`git stash drop`来删除。

也可以用`git stash pop`进行弹栈。

```cmd
$ git stash pop
On branch master
Your branch is ahead of 'origin/master' by 9 commits.
  (use "git push" to publish your local commits)

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   Notes.md

no changes added to commit (use "git add" and/or "git commit -a")
Dropped refs/stash@{0} (b7eb343b0be8fb45e6a6f35436712a6e912a368c)
```



由于`dev`是从`master`分化出来的，因此`dev`中也存在着相同的bug（`dev`分支中的test.txt还是this is file）。同样的bug，要在`dev`上修复，我们只需要把``这个提交所做的修改“复制”到`dev`分支。

注意：我们只想复制`[issue-101 2f0b6e2] 修Bug`这个**提交**所做的修改，并不是把整个`master`分支merge过来。为了方便操作，Git专门提供了一个`cherry-pick`命令，让我们能复制一个特定的提交到当前分支

```cmd
$ git cherry-pick 2f0b6e2
[dev df9a32d] 修Bug
 Date: Sat Jul 27 16:42:40 2024 +0800
 1 file changed, 1 insertion(+), 1 deletion(-)
```

Git自动给`dev`分支做了一次提交。注意这次提交的commit是`df9a32d`，它并不同于`master`的`2f0b6e2`，因为这两个commit只是改动相同，但确实是**两个不同的commit**。用`git cherry-pick`，我们就不需要在`dev`分支上手动再把修bug的过程重复一遍。

### 5.5 Feature分支

每添加一个新功能，最好新建一个feature分支，在上面开发，完成后，合并，最后，删除该feature分支。如：

```cmd
$ git checkout feature-music
```

### 5.6 多人协作

[多人共用一个远程库时](https://liaoxuefeng.com/books/git/branch/collaboration/index.html)

[Rebase](https://liaoxuefeng.com/books/git/branch/rebase/index.html)

## 6. 标签管理

发布一个版本时，我们通常先在版本库中打一个标签（tag），这样，就唯一确定了打标签时刻的版本。tag就是一个让人容易记住的有意义的名字，它跟某个commit绑在一起。

### 6.1 创建标签

在需要打标签的分支上键入`git tag`就能添加新标签

```cmd
$ git tag v1.0
```

使用`git tag`查看所有标签。

也可以根据commit id打标签。先用`git log`查看所有commit id

```cmd
$ git tag v0.9 f52c633
```

注意，标签不是按时间顺序列出，而是按字母排序的。可以用`git show <tagname>`查看标签信息：

```cmd
$ git show v0.9
commit f52c63349bc3c1593499807e5c8e972b82c8f286 (tag: v0.9)
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:56:54 2018 +0800

    add merge

diff --git a/readme.txt b/readme.txt
...
```

还可以创建带有说明的标签，用`-a`指定标签名，`-m`指定说明文字：

```cmd
$ git tag -a v0.1 -m "version 0.1 released" 1094adb
```

### 6.2 删除标签

```cmd
$ git tag -d v0.1
```

如果要推送某个标签到远程，使用命令`git push origin <tagname>`：

```cmd
$ git push origin v1.0
```

或者，一次性推送全部尚未推送到远程的本地标签：

```cmd
$ git push origin --tags
```

如果标签已经推送到远程，要删除远程标签就麻烦一点，先从本地删除：

```cmd
$ git tag -d v0.9
```

然后，从远程删除。删除命令也是push，但是格式如下：

```cmd
$ git push origin :refs/tags/v0.9
To github.com:michaelliao/learngit.git
 - [deleted]         v0.9
```

要看看是否真的从远程库删除了标签，可以登陆GitHub查看。

## 7. 忽略特殊文件

忽略特殊文件https://liaoxuefeng.com/books/git/customize/ignore/index.html

## 8. 配置命令别名——缩写

https://liaoxuefeng.com/books/git/customize/alias/index.html

- `git lg`：格式化git log`git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"`

- `git last`：显示最后一次提交信息`$ git config --global alias.last 'log -1'`

## 9. 搭建Git服务器

https://liaoxuefeng.com/books/git/customize/server/index.html

















在·
