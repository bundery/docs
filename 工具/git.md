# Git
Git是一种用于管理**文本文件**的版本控制系统，无法追踪二进制文件的变化。

## 创建本地版本库
1. 创建一个文件夹。
2. 通过`git init`命令把这个目录变成 Git 可以管理的仓库，当前的目录下就多了一个隐藏的 `.git` 目录，这个目录是 Git 用来跟踪管理版本库的。
3. 把文件添加到仓库：
    - 用命令`git add`告诉 Git，把文件添加到暂存区。`$ git add readme.txt`。
    - 用命令`git commit`告诉Git，把文件提交到仓库。`$ git commit -m "write a readme file" `。`-m`输入的是本次提交的说明，内容要有意义，这样就可以方便的从历史记录里找到改动记录了。由于`git commit`可以一次提交很多文件，所以可以多次`git add`不同的文件，所以 Git 添加文件需要`git add&git commit`两步,比如：

```shell
    $ git add file1.txt
    $ git add file2.txt file3.txt
    $ git commit -m "add 3 files"
```

## 关联远程版本库

比如在 github 网站上创建一个空的 repository (版本仓库)，然后与本地版本库关联，本地版本库可以通过以下命令关联多个远程版本库：`$ git remote add origin git@github.com:xxx/xxx.git`，添加后远程库的名字就是`origin`，这是 Git 默认的叫法。

## 创建SSH Key

在用户目录下 ("C:\Users\jerry") 执行以下命令，创建好后有一个.ssh目录

```shell
$ ssh-keygen -t rsa -C "bandery@mail.com"
```

把公钥的内容上传到 github 等网站，私钥要保存到本地不能泄露，这样就可以进行本机到服务器间的通讯了。

## Commit管理
commit 的版本号`commit id`是一个 SHA1 计算出来的非常长的数字（防止重复）。每提交一个版本，git 就会自动把它们串成一条线。在 git 中，用`HEAD`表示当前版本，也就是最新的 commit，上一个版本就是`HEAD^`，上 100 个版本就是`HEAD~100`

- 把当前版本回退到上一个版本:`$ git reset --hard HEAD^`，之后再用`git log`命令就找不到之后的commit记录了，不过只要知道之前的 commit_id，就能恢复。
- `git checkout -- file`：文件已经修改但未 add 到暂存区，想直接丢弃工作区的修改时，用此命令可还原。
- `git reset HEAD file`：文件已经修改且已 add 到暂存区但未 commit 到版本库，想丢弃修改；分两步：第一步用此命令就回到了上部分状态，第二部按上部分命令操作。
- `git reset --hard HEAD^`：已经提交了不合适的修改到版本库时，想要撤销本次提交，就用此命令进行版本回退。
- `git rm test.txt` 从版本库中删除该文件，并且之后再用`git commit -m "remove test.txt"`提交，文件就从版本库中被删除了。

## 分支管理

- 查看当前分支：`git branch`
- 查看所有分支: `git branch -a`
- 创建新分支：`git branch branch_name`
- 切换到指定分支：`git checkout branch_name`
- 创建+切换到分支：`git checkout -b branch_name`
- 合并指定分支到当前分支分支：`git merge branch_name`
- 合并某个与当前分支没有共同基础 commit 的分支到当前分支：`git merge branch_name --allow-unrelated-histories`
- 删除分支：`git branch -d branch_name`
- 删除没有合并过的分支：`git branch -D branch_name`

### git fetch
如果远程版本库有了更新（新的 commit ），需要将这些更新取回本地，就可以使用`git fetch <远程主机名>`命令，默认情况下，git fetch 取回所有分支的更新，如果只想取回特定分支的更新，可以使用`git fetch <远程主机名> <分支名>`，如`git fetch origin master`用来取回origin主机的 master 更新。在本地主机读取取回的更新只能用“远程主机名/分支名”的形式读取，如`origin/master`。

取回更新后可以使用以下命令：

- `git checkout -b branch_name origin/master`：在远程分支基础上创建一个新的分支。
- `git merge origin/master`：合并远程分支到当前分支（保留旧分支信息）
- `git rebase origin/master`：合并远程分支到当前分支 （不保留旧分支信息）

### git pull
用于取回远程主机上某个分支的更新，再与本地的指定分支合并，实际上 git pull 相当于先执行 git fetch，再执行 git merge 。

```shell
git pull <远程主机名> <远程分支名>:<本地分支名>
```

- 如果远程分支与当前分支合并，可以省略冒号后的内容
- 如果远程分支与本地分支没有共同的基础，默认情况下会出现`fatal: refusing to merge unrelated histories`错误，需要在后面添加`--allow-unrelated-histories`参数

### git push
用于推送本地仓库中本地分支的更新推送到远程仓库。

```shell
$ git push <远程主机名> <本地分支名>:<远程分支名>
```

- push 和 pull 命令遵循<来源地>:<目的地>。
- 如果省略远程分支名，如:`git push -u origin master`，则表示将本地分支推送至与之存在“追踪关系”（一般同名）的远程分支，如果不存在则会创建。
- `-u`参数用于指定默认远程主机，在以后的推送或者拉取时就可以简化命令，如`git push`把直接当前分支推送到默认远程主机的关联分支。
- 如果远程库的版本比本地版本新，push 时会报错，此时要先把远程库中的新内容`git pull`到本地，然后再push。

### 分支合并冲突
当俩个分支的相同文件的相同代码块不一致时，git 就不能自动合并了，称为冲突 (confilct) 需要手动解决冲突后提交（把冲突的文件选择一个分支的版本，然后 commit），就能合并了。
修改冲突文件，然后执行：

```shell
    $ git add readme.txt
    $ git commit -m "confict fixed"
```

最后删除不需要的分支`git branch -d gh-pages`

## 标签管理
发布一个版本时，我们通常先在版本库中打一个标签（tag），这样，就唯一确定了打标签时刻的版本。将来无论什么时候，取某个标签的版本，就是把那个打标签的时刻的历史版本取出来。所以，标签也是版本库的一个快照。Git 的标签虽然是版本库的快照，但其实它就是指向某个 commit 的指针（跟分支很像，但是分支可以移动，标签不能移动），tag 相比 commit 是一个更有意义的名字，跟某个 commit 绑定在一起。

- 创建默认标签：首先切换到需要打标签的分支上，然后`git tag tag_name`创建新标签（默认指向最新的 commit）
- 在指定的 commit 创建标签：`git tag tag_name commit_id`(如`git tag v0.9 6224937`)
- 带有说明的标签：`git tag -a v0.1 -m "version 0.1 released" 3628164` `-a`指定标签名，`-m`指定说明文字
- 查看所有标签：`git tag`
- 查看标签信息：`git show tag_name`

## 忽略文件

- 忽略某些文件时，需要便携`.gitgnore`
- `.gitgnore`文件本身要放到版本库里，并且可以对`.gitgnore`做版本管理
- [各种.gitnore模板](https://github.com/github/gitignore)

## 其他命令

- `git status`：可以让我们时刻掌握仓库当前的状态，可以告诉文件有修改过
- `git diff readme.txt`：查看文件具体修改了什么内容
- `git log`：查看commit的历史记录（版本回退时，确定回到哪个版本）
- `git reflog`：查看所有的命令记录（确定回到未来的哪个版本）