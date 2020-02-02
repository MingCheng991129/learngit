# Git学习笔记

## 创建版本库

1. 查看文件夹所在目录：`pwd`

2. 初始化Git仓库：`git init`

3. 添加文件到Git仓库，分为两步：

   (以readme.txt为例)

   1. `git add readme.txt` (可以反复使用，添加多个文件)
   2. `git commit -m “wrote a txt file”`

## 时光机穿梭

1. `git status`： 查看仓库当前状态
2. `git diff readme.txt`：查看difference

### 版本回退

1. `git log`命令显示从最近到最远的提交日志

2. 使用`git reset`命令：版本回退 

   在Git中，用`HEAD`表示当前版本，也就是最新的提交，上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，当然往上100个版本写100个`^`比较容易数不过来，所以写成`HEAD~100`。

3. 从旧版本回到新版本：需要知道commit id。

   `git reflog`记录了每一次的命令，从而可以查看commit id。

   `git reset --hard 3525`：回到commit id为3525的版本。

### 工作区和暂存区

工作区：电脑里能看到的目录，如`learngit`文件夹

版本库（repository）：版本库里面包括：stage暂存区，master分支，HEAD指针等。

新建立的文件但是还没有被添加（add）的状态为：`Untracked`

`git add`命令：把所有要提交的修改放到stage暂存区

`git commit`命令：把暂存区的所有内容提交到当前分支

### 管理修改

**每次修改，如果不用`git add`到暂存区，就不会加入到`commit`当中。**

查看版本库里面版本和工作区里面版本的区别(有时候会修改了文件但是没有add)：`git diff HEAD -- readme.txt`命令 （HEAD指针在版本库里面，工作区为readme.txt）

### 撤销修改

**文件还没有add：**

使用git checkout命令：`git checkout -- readme.txt` （**注意--不能忘记**）

让这个文件回到最近一次`git commit`或`git add`时的状态。

**文件已经add，还没有commit：**

`git reset HEAD readme.txt`：把暂存区的修改撤销掉，重新放回到工作区，当我们用`HEAD`时，表示**最新的**版本。

然后再`git checkout -- readme.txt`丢弃**工作区**的修改。



**小结：**

场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。

场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`，就回到了场景1，第二步按场景1操作。

### 删除文件

假设现在要删除文件test.txt（该文件已经被添加到版本库中）

如果用命令`rm test.txt`，则工作区中该文件被删掉了，但是版本库中还没有。

**若在版本库中也要删除该文件：**

步骤1：`git rm test.txt`

步骤2：`git commit -m “remove test.txt”`

**若要从版本库中恢复到工作区中：**

`git checkout -- test.txt`

**实际上，`git checkout`命令是用版本库里面的版本替换工作区里面的版本。**

## 远程仓库

创建SSH Key：`ssh-keygen -t rsa -C "youremail@example.com"`

### 添加远程库

关联一个远程库：使用命令：`git remote add origin git@server-name:path/repo-name.git`，若使用GitHub，则`server-name`应该为github.com，`path/repo-name`为GitHub上仓库的路径。

**第一次**推送master分支上的所有内容：`git push -u origin master`

此后，推送最新修改：`git push origin master`

###  从远程库克隆

`git clone git@github.com:xxx/repo_name.git`

## 分支管理

### 创建与合并分支

`HEAD`严格来说不是指向提交，而是指向`master`，`master`才是指向提交的，所以，`HEAD`指向的就是当前分支。

1. 创建并切换到新的分支dev：`git checkout -b dev`

   切换分支的其他命令：

   `git switch -c dev`：创建并切换到**新的**dev分支

   `git switch master`：切换到**已有的**master分支

2. `git branch`命令查看当前分支

3. 合并分支：`git merge master`（合并指定分支(master)到当前分支(dev)）

4. 删除分支：`git branch -d dev`（删除分支dev）

### 解决冲突

当无法合并分支时，需要先解决冲突，再提交，合并完成。

查看分支合并图：`git log –-graph`，在一行当中显示：`git log --graph --pretty=oneline --abbrev-commit`

### 分支管理策略

从分支历史上看分支信息：禁用`Fast forward`模式，Git就会在合并分支时生成新的commit。使用`--no-ff`方式。

**`Fast forward`方式能看到曾经做过合并，`-–no-ff`方式看不出做过合并。**

例如，merge时使用`git merge --no-ff -m “merge with no-ff”`后，便可以用`git log`查看历史。

### 分支策略

实际开发过程中，`master`分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；应该在新创建的分支上干活。

### Bug分支

修复bug时，应该创建新的分支来进行修复，然后合并，最后删除。

当手头工作还没有commit时，先把工作现场`git stash`一下，然后修复bug，修复完之后再`git stash pop`，回到工作现场。

命令`git stash list`查看所有已经保存的工作现场。可以通过`git stash apply stash@{0}`恢复指定的stash。

在master分支上修复的bug，想要合并到当前dev分支，用`git cherry-pick 4c805e2`命令，其中`4c805e2`为之前修复bug的commit id。





