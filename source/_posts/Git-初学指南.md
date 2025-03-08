---
title: Git 初学指南
date: 2023-12-08 12:18:02
tags:
- Git
categories:
- Git
---
学习 git 记录
官方地址：https://learngitbranching.js.org/?locale=zh_CN

# 介绍

1. Git 网络传输从 SSH 到 HTTP
2. 防止数据丢失：分布式版本控制系统（DVCS），客户端每一次克隆操作，实际上都是一次对代码仓库的完整备份

### 特点
1. 直接记录快照，而非比较差异
2. 近乎所有操作都是本地执行，历史信息
3. 保证完整性，计算校验和 SHA-1 散列（hash，哈希），保存的信息以文件内容的哈希值来索引
4. 一般只添加数据

### 三种状态
- 已提交（committed） 表示数据已经安全地保存在本地数据库中
- 已修改（modified） 表示修改了文件，但还没保存到数据库中
- 已暂存（staged） 表示一个已修改文件的当前版本做了标记，使之包含在下次提交的快照中

### 三个阶段
- 工作区（working directory） 是对某个版本独立提取出来的内容，存放磁盘
- 暂存区（staging area） 是一个文件，保存了下次将要提交的文件列表信息，也叫索引
- Git 目录（.git directory(repository)） Git 保存项目的元数据和对象数据库的地方

### 工作流程
- 在工作区修改文件
- 将需要提交的文件选择性地添加到暂存区
- 提交更新，将快照永久性存储到 git 目录

## 命令行相关

### 查找所有配置相关

`git config --list --show-origin`

### 设置用户名和邮件地址

`git config --global user.name "xukai"`

`git config --global user.email wb.xukai03@mesg.corp.netease.com`

### 获取帮助
```
git help <verb>
git <verb> --help
git man git-<verb>
git help config
git add -h
```

### 两种获取 git 仓库的方式
1. 将尚未进行版本控制的本地目录转换为 git 仓库
   1. 命令行进入目录 `cd /d D:` `cd D://MyGit`
   2. 初始化仓库 `git init` 该命令会创建一个 **.git** 的子目录
   3. 添加指令 `git add *.c` `git add LICENSE`
   4. 提交指令 `git commit -m 'initial project version'`
2. 从其他服务器克隆一个已存在的 git 仓库
   1. 克隆 `git clone https://github.com/armour07/learn.git`
   2. 克隆并指定名字 `git clone https://github.com/armour07/learn.git mylearn`

生成新的 SSH 密钥对绑定到仓库
1. 生成新的 SSH 密钥对，其中包括一个私钥和一个公钥
`ssh-keygen -t rsa -b 4096 -C "<your_email@example.com>"`
2. 复制 ~/.ssh/id_rsa.pub 的内容保存到仓库中，~表示用户文件夹，一般位于C:/用户/用户名/

-t rsa 表示使用 RSA 算法生成 SSH 密钥对。RSA 是一种非对称加密算法，它使用公钥和私钥来加密和解密数据。在 SSH 中，公钥用于验证您的身份，私钥用于对数据进行加密和解密。

-b 4096 表示生成的 RSA 密钥长度为 4096 位。密钥长度越长，安全性就越高，但生成和使用密钥的成本也越高。4096 位的 RSA 密钥是目前最常用的安全密钥长度之一。

-C "<your_email@example.com>" 表示在生成密钥对时，将您的电子邮件地址作为注释添加到公钥中。注释是可选的，但它可以帮助您识别和管理多个 SSH 密钥对。在使用 SSH 密钥时，注释通常会显示在 SSH 客户端的提示信息中，以帮助您确认您正在使用正确的密钥。

克隆支持协议：
1. `https://`
2. `git://`
3. `SSH:`

## 指令介绍

### add
`git add` 命令用于将工作目录中的修改添加到 Git 的暂存区中。它是 Git 中最基本的命令之一，也是 Git 工作流程中的第一步。

常用的 `git add` 命令选项如下：

   `git add <file>`：将指定的文件添加到暂存区中。
   `git add -A 或 git add .`：将所有修改（包括新增、修改和删除）添加到暂存区中。
   `git add -u`：将所有修改（包括修改和删除，但不包括新增）添加到暂存区中。

### rm
`git rm` 命令用于从 Git 仓库中删除文件。它会将指定的文件从工作目录和暂存区中删除，并将删除操作记录到 Git 仓库中。

常用的 `git rm` 命令选项如下：

   `git rm <file>`：从 Git 仓库中删除指定的文件。
   `git rm -r <directory>`：从 Git 仓库中递归删除指定的目录及其下的所有文件。
   `git rm --cached <file>`：从 Git 仓库中删除指定的文件，但保留工作目录中的文件。

需要注意的是，使用 `git rm` 命令删除文件或目录后，需要使用 `git commit` 命令提交这个变更，才能将删除操作记录到 Git 的提交历史中。

另外，如果误删了文件或目录，可以使用 `git checkout` 命令恢复被删除的文件或目录。例如，使用 `git checkout <file>` 命令可以恢复被删除的文件，使用 `git checkout <directory>` 命令可以恢复被删除的目录及其下的所有文件和子目录。

### restore
`git restore` 命令用于撤销工作目录中的修改，将文件恢复到最近一次提交的状态。它可以用于撤销对文件的修改、删除和重命名等操作。

常用的 `git restore` 命令选项如下：

   `git restore <file>`：撤销指定文件的修改，将其恢复到最近一次提交的状态。
   `git restore --staged <file>`：撤销指定文件的暂存操作，将其恢复到最近一次提交的状态。
   `git restore --source=<commit> <file>`：将指定文件恢复到指定提交的状态。

### mv
`git mv` 命令用于移动或重命名 Git 仓库中的文件或目录，并将这些操作提交到 Git 历史记录中。

常用的 `git mv` 命令选项如下：

   `git mv <source> <destination>`：其中，<source> 表示要移动或重命名的文件或目录的原路径，<destination> 表示它们的目标路径。如果目标路径已经存在，则会覆盖它。

### commit
`git commit` 命令用于将暂存区中的修改提交到本地仓库中。每次提交都会生成一个新的提交对象，包含了当前修改的快照和一些元数据，如作者、提交时间、提交信息等。

常用的 `git commit` 命令选项如下：

`git commit`：将暂存区中的修改提交到本地仓库中，并打开编辑器以便输入提交信息。
`git commit -m "<message>"`：将暂存区中的修改提交到本地仓库中，并使用指定的提交信息。
`git commit -a`：将工作目录中的所有修改暂存起来，并将暂存区中的修改提交到本地仓库中。
`git commit --amend`：修改最近一次提交的提交信息或者添加新的修改到最近一次提交中。

### stash
`git stash` 命令用于将当前工作目录中的修改暂存起来，以便在以后的某个时间点恢复这些修改。这个命令可以在你需要切换分支或者暂时不想提交修改时使用。

`git stash` 命令会将当前工作目录中的修改保存到一个栈中，并将工作目录恢复到上一次提交的状态。你可以在任何时候使用 `git stash apply` 命令来恢复这些修改。

常用的 `git stash` 命令选项如下：

   `git stash`：将当前工作目录中的修改暂存起来。
   `git stash save "<message>"`：将当前工作目录中的修改暂存起来，并添加一条描述信息。
   `git stash list`：显示当前保存的所有暂存记录。
   `git stash apply`：恢复最近一次保存的暂存记录。
   `git stash apply <stash>`：恢复指定的暂存记录。
   `git stash drop`：删除最近一次保存的暂存记录。
   `git stash drop <stash>`：删除指定的暂存记录。
   `git stash pop`：恢复最近一次保存的暂存记录，并删除该记录。
   `git stash pop <stash>`：恢复指定的暂存记录，并删除该记录。

### status
`git status` 命令用于显示当前工作目录和暂存区的状态。它可以用于查看哪些文件被修改、哪些文件被暂存、哪些文件未被跟踪等信息。

常用的 `git status` 命令选项如下：

   `git status -s` 或 `git status --short`：以简短的格式输出状态信息。每个文件的状态用两个字符表示，第一个字符表示暂存区的状态，第二个字符表示工作区的状态。
   `git status -b` 或 `git status --branch`：同时显示当前所在的分支名称。
   `git status --show-stash`：显示当前仓库中的 stash 列表。
   `git status --ignored`：显示被 Git 忽略的文件列表。

### log
`git log` 命令用于显示当前分支的提交历史记录。它可以显示每个提交的作者、提交时间、提交信息等信息，并按照提交时间的先后顺序进行排序。

常用的 `git log` 命令选项如下：

   `git log`：显示当前分支的提交历史记录。
   `git log <commit>`：显示指定提交及其之前的提交历史记录。
   `git log --oneline`：以一行的形式显示提交历史记录。
   `git log --graph`：以图形化的形式显示提交历史记录。
   `git log --author=<author>`：只显示指定作者的提交历史记录。
   `git log --since=<date>`：只显示指定日期之后的提交历史记录。
   `git log --until=<date>`：只显示指定日期之前的提交历史记录。

### branch
`git branch` 是 Git 中用于管理分支的命令。它可以列出当前仓库中所有的分支，创建新的分支，删除分支，重命名分支等。

常用的 `git branch` 命令选项如下：

   `git branch`：列出当前仓库中所有的分支，当前分支前面会有一个星号标记。
   `git branch <branch-name>`：创建一个新的分支，分支名为 `<branch-name>`。
   `git branch -d <branch-name>`：删除指定的分支，如果该分支还有未合并的提交，会提示错误信息。
   `git branch -D <branch-name>`：强制删除指定的分支，即使该分支还有未合并的提交。
   `git branch -m <old-branch-name> <new-branch-name>`：将指定的分支重命名为 `<new-branch-name>`。
   `git branch -a`：列出所有本地和远程的分支。
   `git branch -v`：列出所有分支及其最后一次提交的信息。

### checkout
`git checkout` 是 Git 中用于切换分支或恢复文件的命令。它可以用于切换到已存在的分支，创建新的分支并切换到该分支，恢复工作目录中的文件等。

常用的 `git checkout` 命令选项如下：

   `git checkout <branch-name>`：切换到指定的分支。
   `git checkout -b <new-branch-name>`：创建一个新的分支并切换到该分支。
   `git checkout <commit-hash> <file-name>`：恢复指定提交中的文件到工作目录中。
   `git checkout -- <file-name>`：撤销对指定文件的修改，将其恢复到最近一次提交的状态。
   `git branch -r`：查看所有远程分支。
   `git fetch origin <remote-branch-name>:<local-branch-name>`：拉取远程分支到本地仓库。

### merge
`git merge` 是 Git 中用于合并分支的命令。它可以将一个分支的修改合并到另一个分支中，从而将两个分支的修改合并为一个新的提交。

常用的 `git merge` 命令选项如下：

   `git merge <branch-name>`：将指定分支的修改合并到当前分支中。
   `git merge --no-ff <branch-name>`：禁用快进合并，强制创建一个新的合并提交。
   `git merge --abort`：取消当前的合并操作，恢复到合并之前的状态。

### reset
`git reset` 命令用于将当前分支的 HEAD 指针移动到指定的提交，并将工作区和暂存区的代码恢复到指定提交的状态。它可以用于撤销提交、取消暂存的文件、修改最后一次提交的提交信息等操作。

常用的 `git reset` 命令选项如下：

   `git reset <commit>`：将当前分支的 HEAD 指针移动到指定的提交，并将工作区和暂存区的代码恢复到指定提交的状态。
   `git reset --hard <commit>`：将当前分支的 HEAD 指针移动到指定的提交，并强制将工作区和暂存区的代码恢复到指定提交的状态，丢弃所有未提交的修改。
   `git reset --soft <commit>`：将当前分支的 HEAD 指针移动到指定的提交，但不修改工作区和暂存区的代码，保留所有未提交的修改。
   `git reset HEAD <file>`：取消暂存指定的文件，将其从暂存区移除。
   `git reset --hard HEAD`：将工作区和暂存区的代码恢复到最后一次提交的状态，丢弃所有未提交的修改。
   `git reset HEAD~`：撤销最后一次提交，并保留修改。

### rebase

`git rebase main` 合并分支，把当前分支的一个个记录复制一份到 main 中，并将当前分支指向 main 的后续节点

`git rebase main bugFix` 相当于 checkout main 然后 merge bugFix

`git rebase -i HEAD~n`
rebase -i是一个交互式的命令，可以让您对提交历史进行修改。它的全称是git rebase --interactive。

使用rebase -i可以实现以下功能：
1. 修改提交日志：可以修改之前的提交日志，使其更加准确和清晰。
2. 合并提交记录：可以将多个提交记录合并成一个，减少提交历史的数量。
3. 重新排序提交记录：可以按照自己的需求重新排序提交记录。

您可以对每个提交记录进行以下操作：
- pick：保留该提交记录。
- edit：修改该提交记录的内容。
- squash：将该提交记录合并到前一个提交记录中。
- fixup：将该提交记录合并到前一个提交记录中，但不保留该提交记录的日志信息。
- reword：修改该提交记录的日志信息。
- drop：删除该提交记录。

合并时，会出现改文本，可移除#2内容
```
# This is a combination of 2 commits.
# This is the 1st commit message:

<commit-message-1>

# This is the commit message #2:

<commit-message-2>
```

### cherry-pick
`git cherry-pick` 命令用于将指定的提交应用到当前分支上。它可以将其他分支或提交中的某个提交应用到当前分支上，从而实现代码复用和合并的效果。

常用的 `git cherry-pick` 命令选项如下：

   `git cherry-pick <commit>`：将指定的提交应用到当前分支上。
   `git cherry-pick -n <commit>`：将指定的提交应用到当前分支上，但不自动提交。
   `git cherry-pick -x <commit>`：将指定的提交应用到当前分支上，并在提交信息中添加来源信息。
   `git cherry-pick -e <commit>`：将指定的提交应用到当前分支上，并打开编辑器以编辑提交信息。

使用`cherry-pick`命令时，Git会将指定的提交应用到当前分支上，并生成一个新的提交。这个新的提交和原来的提交是相同的，但它的父提交是当前分支的最新提交。这样就可以将某个提交应用到当前分支上，而不需要合并整个分支。

需要注意的是，如果要应用的提交和当前分支的最新提交有冲突，那么`cherry-pick`命令会停止，并提示你解决冲突后再继续。此时，你需要手动解决冲突，并使用`git add`命令将解决后的文件添加到暂存区，然后使用`git cherry-pick --continue`命令继续应用提交。

另外，如果要应用的提交已经存在于当前分支中，那么`cherry-pick`命令会停止，并提示你提交已经存在。此时，你可以使用`git cherry-pick --skip`命令跳过这个提交，或者使用`git cherry-pick --abort`命令取消应用提交。

### fetch
`git fetch` 命令用于从远程仓库获取最新的提交记录，但不会自动合并到本地分支。它会将远程仓库的提交记录下载到本地仓库中，但不会修改本地分支的代码。

常用的 `git fetch` 命令选项如下：

   `git fetch`：从远程仓库获取最新的提交记录。
   `git fetch <remote>`：从指定的远程仓库获取最新的提交记录。
   `git fetch --all`：从所有远程仓库获取最新的提交记录。
   `git fetch --prune`：从远程仓库获取最新的提交记录，并删除本地不存在的远程分支。

### pull
`git pull` 命令用于从远程仓库拉取最新的代码并合并到本地仓库中。它实际上是 `git fetch` 和 `git merge` 命令的组合，先拉取远程仓库的最新代码，然后将其合并到当前分支中。

常用的 `git pull` 命令选项如下：

   `git pull`：从远程仓库拉取最新的代码并合并到当前分支中。
   `git pull --rebase`：使用 rebase 而不是 merge 来合并代码。
   `git pull --no-commit`：拉取代码后不自动提交合并结果。

### remote
`git remote` 命令用于管理 Git 仓库中的远程仓库。它可以列出、添加、删除和重命名远程仓库。

常用的 `git remote` 命令选项如下：

   `git remote`：列出所有远程仓库的名称。
   `git remote -v`：列出所有远程仓库的名称和 URL。
   `git remote add <name> <url>`：添加一个新的远程仓库。
   `git remote rename <old-name> <new-name>`：重命名一个远程仓库。
   `git remote remove <name>`：删除一个远程仓库。

### push
`git push` 命令用于将本地仓库中的代码推送到远程仓库中。它将本地仓库中的代码上传到远程仓库中，并更新远程仓库中的代码。

常用的 `git push` 命令选项如下：

   `git push`：将当前分支的代码推送到远程仓库中。
   `git push <remote> <branch>`：将指定分支的代码推送到远程仓库中。
   `git push --force`：强制推送代码，覆盖远程仓库中的代码。
   `git push --tags`：将本地仓库中的标签推送到远程仓库中。
   `git push -u <remote> <branch>`：将本地分支与远程分支关联起来。

### tag
`git tag` 命令用于管理 Git 仓库中的标签。标签是一个指向 Git 仓库中某个特定提交的引用，通常用于标记发布版本或重要的里程碑。

常用的 `git tag` 命令选项如下：

   `git tag`：列出所有标签。
   `git tag <tagname>`：创建一个新的标签。
   `git tag -a <tagname> -m <message>`：创建一个带注释的标签。
   `git tag -d <tagname>`：删除指定的标签。
   `git push <remote> <tagname>`：将指定的标签推送到远程仓库中。
   `git push --tags`：将本地仓库中的所有标签推送到远程仓库中。

### diff
`git diff` 命令用于比较 Git 仓库中不同版本之间的差异。它可以比较工作区和暂存区之间的差异，也可以比较暂存区和最新提交之间的差异，还可以比较任意两个提交之间的差异。

常用的 `git diff` 命令选项如下：

   `git diff`：比较工作区和暂存区之间的差异。
   `git diff --cached`：比较暂存区和最新提交之间的差异。
   `git diff <commit1> <commit2>`：比较任意两个提交之间的差异。

### 忽略提交文件
忽略文件检测，需要建立一个名为 .gitignore 文件位于根目录，列出忽略的文件
```
*.[ao]
*~
```
第一行表示忽略 *.a 或者 *.o
第二行表示忽略所有以 ~ 结尾的文件

- 空行或 # 开头行都会被忽略
- 目录开头加开头'/' 表示只检测根目录的文件夹，防止递归，/a 表示忽略 /a 但不忽略 /b/a
- 目录结尾加'/' 表示忽略所有同名目录，a/ 表示忽略 /a 并且忽略 /b/a
- 前面加'!' 表示取反 *.txt 并且 !a.txt 表示忽略所有.txt 但不忽略 a.txt
- (*) 匹配零个或多个任意字符
- ([abc]) 匹配任何一个在方括号里的字符
- (?) 匹配任意一个字符
- ([0-9]) 匹配 0-9的数字
- (a/**/z) 匹配任意中间目录，如 a/z, a/b/z, a/b/c/z

### 技巧

修改旧日志
```
git rebase -i HEAD~2 先交换位置
git commit --amend   再修改最后一条日志
git rebase -i HEAD~2 在恢复位置
```

新建仓库并关联远程仓库
```
git init
git remote add origin <url>
git push -u origin master
```

新建本地分支并推送到远程创建分支
```bash
git checkout -b my_branch
git push -u origin my_branch
# 修改内容
git push
```


### Vim 文本编辑器：
1. 在编辑器界面中，按下 `i` 键进入插入模式。
2. 输入提交信息，描述本次提交的内容和目的。
3. 按下 `Esc` 键退出插入模式。
4. 输入 `ZZ` `:wq` `:wq!` 命令保存并退出编辑器。
5. 提交操作完成。