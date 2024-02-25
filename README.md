# Tool-Chain
各种工具命令的使用指南

- [x] [Git](#git)
  - [Git 原理图](#git-原理图)
  - [多人协作](#多人协作)
  - [忽略某些文件](#忽略某些文件)
- [x] [Docker](#docker)
- [x] [Valgrind](#valgrind-内存泄露分析工具)
- [x] [Perf](#perf-性能分析工具)
- [x] [Wireshark-tcpdump](#wireshark-tcpdump-网络分析工具)

## Git
### Git 原理图
![image-git1](./images/git-1.jpg)
![image-git2](./images/git-2.jpg)
* 安装与用户信息配置
```shell
sudo apt-get install git

git config --global user.name "lizy"
git config --global user.email LizyZZZ@163.com
```
* 常用命令  
```Shell
# (在目录下)创建仓库
git init <dir_name>

# 克隆仓库到指定目录
git clone <repo> <directory>

# 查看当前 git 仓库的状态
git status [-s]

# 显示暂存区和工作区的差异
git diff [file]
# 显示暂存区和上一次提交(commit)的差异
git diff --cached [file]
# 显示两次提交之间的差异
git diff [first-branch]...[second-branch]

# 添加文件到暂存区
git add <file1> <file2> ...
git add .

# 把暂存区的内容提交到本地仓库
git commit -m "content"
# 可以省略 git add
git commit -a

# 回退某一次提交的版本
# --mixed 默认参数: 重置暂存区的文件与上一次的提交(commit)保持一致，工作区文件内容保持不变
# --soft 用于回退到某个版本
# --hard 撤销工作区中所有未提交的修改内容，将暂存区与工作区都回到上一次版本，并删除之前的所有信息提交
git reset [--soft | --mixed | --hard] [HEAD]
git reset --soft HEAD     # 回退到上个版本
git reset --soft HEAD~3   # 回退上上上一个版本 
git reset –hard bae128    # 回退到某个版本回退点之前的所有信息。 

# 从工作区和暂存区(强制/递归)删掉指定文件
git rm [-f][-r] <filename>
# 仅从暂存区删除
git rm --cached <file>

# 移动或重命名一个文件、目录或软连接
git mv [file] [newfile]

# 查看分支
git branch
# 删除本地分支
git branch -d <branch-name>


# 切换分支
git checkout <branch-name>
git switch <branch-name>
# 创建并切换分支
git checkout -b <new-branch-name>
# 切换到前一个分支
git checkout -
# 将指定文件 <file> 恢复到最新的提交状态，丢弃所有未提交的更改，这对于撤销不需要的更改非常有用
git checkout -- <file>

# 合并当前分支和指定分支, 有冲突时需要手动解决冲突
git merge <branch-name>

# 查看提交历史(一行显示一次提交 / 逆序 / 作者)
git log [--oneline][--reverse][--author]

# 逐行显示指定文件的每一行代码是由谁在什么时候引入或修改的
git blame [选项] <文件路径>

# 将本地的分支版本上传到远程并合并
git push <远程主机名> <本地分支名>:<远程分支名>
# 如果本地分支名与远程分支名相同，则可以省略冒号
git push <远程主机名> <本地分支名>
# 删除远程分支
git push origin --delete master

# 从远程获取代码并合并本地的版本
git pull <远程主机名> <远程分支名>:<本地分支名>
# 如果远程分支是与当前分支合并，则冒号后面的部分可以省略
git pull <远程主机名> <远程分支名>
```

### 多人协作
多人协作的工作模式通常是这样：  
* 首先，可以试图用git push origin <branch-name>推送自己的修改；
* 如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；
* 如果合并有冲突，则解决冲突，并在本地提交；
* 没有冲突或者解决掉冲突后，再用git push origin <branch-name>推送就能成功！
* 如果git pull提示no tracking information，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream-to <branch-name> origin/<branch-name>
```Shell
# 克隆远程分支
git clone <repo>

# 当别的小伙伴从远程库clone时，默认情况下，你的小伙伴只能看到本地的master分支
git branch
* main # 只有 main 分支

# 他要在 dev 分支上开发，就必须创建远程 origin 的 dev 分支到本地，用这个命令创建本地 dev 分支
git checkout -b dev origin/dev

# 他可以在 dev 上继续修改，然后，时不时地把 dev 分支 push 到远程
git add env.txt
git commit -m "add env"
git push origin dev

# 当我也修改了 env.txt, 并尝试推送
git add env.txt
git commit -m "add new env"
git push origin dev       # 出现冲突, 推送失败

# 先用 git pull 把最新的提交从 origin/dev 拉下来，然后，在本地合并，解决冲突，再推送
git pull origin dev  # 也失败, 原因是没有指定本地dev分支与远程origin/dev分支的链接
# 根据提示，设置dev和origin/dev的链接
git branch --set-upstream-to=origin/dev dev

# 再尝试 pull
git pull origin dev
# git pull成功，但是合并有冲突，需要手动解决, 解决后再次 commit 和 push
git commit -m "fix env conflict"
git push origin dev


# 或许也可以使用 --no-rebase 来解决没有链接的问题(推荐)
# 使用git pull --no-rebase 时，Git 会使用传统的合并方式(merge)，即将远程分支的更新合并到本地分支上，然后手动解决冲突, 并生成一个合并提交来整合这些更新;

# 使用git pull --rebase 时，Git 会在合并远程分支更新之前，先将当前分支的提交临时保存起来，然后将这些提交依次应用到远程分支的最新提交上
# 当你执行 rebase 操作时，Git 会逐个应用当前分支的每个提交到远程分支的最新提交上。
# 如果在应用某个提交的过程中发现冲突，Git 会暂停 rebase 进程，让你解决冲突。
# 你需要手动解决冲突，然后继续 rebase 进程。这个过程会一直持续直到所有提交都被成功应用
git pull --no-rebase origin dev
```

### 忽略某些文件
`.gitignore` 文件, 支持通配符

## Docker


## Valgrind 内存泄露分析工具


## Perf 性能分析工具


## Wireshark tcpdump 网络分析工具