# Tool-Chain
各种工具命令的使用指南

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
# 删除分支
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

# 查看提交历史(一行显示一次提交 / 逆序 / 作者)
git log [--oneline][--reverse][--author]

# 逐行显示指定文件的每一行代码是由谁在什么时候引入或修改的
git blame [选项] <文件路径>

# 将本地的分支版本上传到远程并合并
git push <远程主机名> <本地分支名>:<远程分支名>
# 如果本地分支名与远程分支名相同，则可以省略冒号
git push <远程主机名> <本地分支名>
# 删除分支
git push origin --delete master

# 从远程获取代码并合并本地的版本
git pull <远程主机名> <远程分支名>:<本地分支名>
# 如果远程分支是与当前分支合并，则冒号后面的部分可以省略
git pull <远程主机名> <远程分支名>
```

### 多人协作
```Shell
# 克隆远程分支
git clone <repo>

# 

```