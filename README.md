# Tool-Chain
各种工具命令的使用指南

- [x] [Git](#git)
  - [Git 原理图](#git-原理图)
  - [安装与用户信息配置](#安装与用户信息配置)
  - [常用命令](#常用命令)
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
### 安装与用户信息配置
```shell
sudo apt-get install git

git config --global user.name "lizy"
git config --global user.email LizyZZZ@163.com
```
### 常用命令  
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


# 子模块管理

# 添加子模块(初始化)
git submodule add <repository_url> <path>
# 初始化子模块
git submodule init 
# 更新子模块
git submodule update [--init][--recursive] 
# 带 --init 参数可以在子模块还没初始化时直接初始化 
# --recursive 参数递归更新子模块

# 查看子模块信息
git submodule status
# 移除子模块
git submodule deinit <path> # 取消对子模块的跟踪
git rm <path>               # 将子模块从当前仓库中移除

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

### Docker 安装
```Shell
# 官方脚本自动化安装
curl -fsSL https://test.docker.com -o test-docker.sh
sudo sh test-docker.sh
# 测试
docker run hello-world
```

### Docker 常用命令
```Shell
# -------------------  镜像相关操作 -----------------------------------
# 在 https://hub.docker.com 免费注册一个, 已注册: lizy66
# 登录需要输入用户名和密码，登录成功后，我们就可以从 docker hub 上拉取自己账号下的全部镜像
docker login
# 退出
docker logout

# 查找官方仓库中的镜像
docker search <img_name>

# 将官方 ubuntu 镜像下载到本地
docker pull <img_name>

# 查看 镜像
docker images [OPTIONS] [REPOSITORY[:TAG]]
# -a :列出本地所有的镜像(含中间映像层，默认情况下，过滤掉中间映像层)；
# --digests :显示镜像的摘要信息；
# -f :显示满足条件的镜像；
# --format :指定返回值的模板文件；
# --no-trunc :显示完整的镜像信息；
# -q :只显示镜像ID

# 根据 Dockerfile 创建镜像
# 使用当前目录的 Dockerfile 创建镜像，标签为 runoob/ubuntu:v1
docker build -t runoob/ubuntu:v1 . 
# 通过 -f Dockerfile 文件的位置：
docker build -f /path/to/a/Dockerfile .

# 删除镜像
docker rmi <IMAGE>

# 标记本地镜像，将其归入某一仓库。
docker tag [OPTIONS] IMAGE[:TAG] [REGISTRYHOST/][USERNAME/]NAME[:TAG]
# 将镜像 ubuntu:15.10 标记为 runoob/ubuntu:v3 镜像
docker tag ubuntu:15.10 runoob/ubuntu:v3

# 从容器创建一个新的镜像
docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
# -a :提交的镜像作者；
# -c :使用Dockerfile指令来创建镜像；
# -m :提交时的说明文字；
# -p :在commit时，将容器暂停。
# 将容器a404c6c174a2 保存为新的镜像,并添加提交人信息和说明信息
docker commit -a "runoob.com" -m "my apache" a404c6c174a2  mymysql:v1 

# 将本地的镜像上传到镜像仓库,要先登陆到镜像仓库
docker push [OPTIONS] NAME[:TAG]
# 上传本地镜像 myapache:v1 到镜像仓库中
docker push myapache:v1

# -------------------  容器相关操作 -----------------------------------

# 运行容器(IMAGES 可以是 id)
docker run [options] IMAGE [COMMAND] [ARGS...]
# -a stdin: 指定标准输入输出内容类型，可选 STDIN/STDOUT/STDERR 三项；
# -d: 后台运行容器，并返回容器ID；
# -i: 以交互模式运行容器，通常与 -t 同时使用；
# -P: 随机端口映射，容器内部端口随机映射到主机的端口
# -p: 指定端口映射，格式为：主机(宿主)端口:容器端口
# -t: 为容器重新分配一个伪输入终端，通常与 -i 同时使用；
# --name="nginx-lb": 为容器指定一个名称；
# --dns 8.8.8.8: 指定容器使用的DNS服务器，默认和宿主一致；
# --dns-search example.com: 指定容器DNS搜索域名，默认和宿主一致；
# -h "mars": 指定容器的hostname；
# -e username="ritchie": 设置环境变量；
# --env-file=[]: 从指定文件读入环境变量；
# --cpuset="0-2" or --cpuset="0,1,2": 绑定容器到指定CPU运行；
# -m :设置容器使用内存最大值；
# --net="bridge": 指定容器的网络连接类型，支持 bridge/host/none/container: 四种类型；
# --link=[]: 添加链接到另一个容器；
# --expose=[]: 开放一个端口或一组端口；
# --volume , -v: 绑定一个卷

# 比如:
# 使用docker镜像nginx:latest以后台模式启动一个容器,并将容器命名为mynginx
docker run --name mynginx -d nginx:latest
# 使用镜像 nginx:latest，以后台模式启动一个容器,将容器的 (任何ip)80 端口映射到主机的 80 端口,主机的目录 /data 映射到容器的 /data
# 即另一个主机也可以通过本机 ip 地址的 80 端口访问 docker 容器内部的服务
docker run -p 80:80 -v /data:/data -d nginx:latest
# 绑定容器的 8080 端口，并将其映射到本地主机 127.0.0.1 的 80 端口上
# 只能在本机通过 80 端口访问 docker 内部的8080服务
docker run -p 127.0.0.1:80:8080/tcp ubuntu bash

# 启动一个容器但是不运行它(同 docker run)
docker create [OPTIONS] IMAGE [COMMAND] [ARG...]

# 查看所有容器(-a 显示已经退出的)
docker ps [-a]

# 使用启动一个已停止的容器
docker start <container_id>

# 停止一个容器
docker stop <container>

# 重启一个容器
docker restart <container>

# 进入容器(并执行命令)
docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
# -d :分离模式: 在后台运行
# -i :即使没有附加也保持STDIN 打开
# -t :分配一个伪终端
# 比如: 在容器 mynginx 中以交互模式执行容器内 /root/runoob.sh 脚本:
docker exec -it mynginx /bin/sh /root/runoob.sh
# 在容器 mynginx 中开启一个交互模式的终端:
runoob@runoob:~$ docker exec -it  mynginx /bin/bash
root@b1a0703e41e7:/#

# 用于容器与主机之间的数据拷贝
docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH|-
docker cp [OPTIONS] SRC_PATH|- CONTAINER:DEST_PATH
# -L :保持源目标中的链接
# 将主机/www/runoob目录拷贝到容器96f7f14e99ab的/www目录下
docker cp /www/runoob 96f7f14e99ab:/www/
# 将主机/www/runoob目录拷贝到容器96f7f14e99ab中，目录重命名为www
docker cp /www/runoob 96f7f14e99ab:/www
# 将容器96f7f14e99ab的/www目录拷贝到主机的/tmp目录中
docker cp  96f7f14e99ab:/www /tmp/

# 移除容器
docker rm [OPTIONS] CONTAINER [CONTAINER...]
# -f :通过 SIGKILL 信号强制删除一个运行中的容器。
# -l :移除容器间的网络连接，而非容器本身。
# -v :删除与容器关联的卷。
# 删除所有已经停止的容器：
docker rm $(docker ps -a -q)
# 删除容器 nginx01, 并删除容器挂载的数据卷：
docker rm -v nginx01

# 获取容器/镜像的元数据
docker inspect [OPTIONS] NAME|ID [NAME|ID...]
docker inspect mysql:5.6

# 杀掉一个运行中的容器
docker kill [OPTIONS] CONTAINER [CONTAINER...]
docker kill <container_id>
# -s :向容器发送一个信号
# 杀掉运行中的容器mynginx
docker kill -s KILL mynginx

# 查看容器中运行的进程信息，支持 ps 命令参数
docker top [OPTIONS] CONTAINER [ps OPTIONS]

# 获取容器的日志
docker logs [OPTIONS] CONTAINER
# -f : 跟踪日志输出
# --since :显示某个开始时间的所有日志
# -t : 显示时间戳
# --tail :仅列出最新N条容器日志
# 查看容器mynginx从2016年7月1日后的最新10条日志
docker logs --since="2016-07-01" --tail=10 mynginx
```

### Dockerfile 编写案例
```Dockerfile
# 使用官方 Ubuntu 作为基础镜像
FROM ubuntu:latest

# 作者信息
LABEL maintainer="Your Name <your.email@example.com>"

# 更新软件包列表并安装 Nginx
RUN apt-get update && \
    apt-get install -y nginx && \
    apt-get clean

# 将本地的 HTML 文件复制到 Nginx 默认的网站目录下
COPY index.html /var/www/html/

# 暴露容器的 80 端口，使外部可以访问 Nginx
EXPOSE 80

# 启动 Nginx 服务器
CMD ["nginx", "-g", "daemon off;"]
```
```Shell
# 一些常用参数
docker build -t nginx:v3 .
# 上下文路径，是指 docker 在构建镜像，有时候想要使用到本机的文件（比如复制），docker build 命令得知这个路径后，会将路径下的所有内容打包

# 复制指令，从上下文目录中复制文件或者目录到容器里指定路径
COPY [--chown=<user>:<group>] <源路径1>...  <目标路径>
# [--chown=<user>:<group>]：可选参数，用户改变复制到容器内文件的拥有者和属组。
# 比如
COPY hom* /mydir/

# 为启动的容器指定默认要运行的程序，程序运行结束，容器也就结束。CMD 指令指定的程序可被 docker run 命令行参数中指定要运行的程序所覆盖
# 如果 Dockerfile 中如果存在多个 CMD 指令，仅最后一个生效
CMD <shell 命令> 
CMD ["<可执行文件或命令>","<param1>","<param2>",...] # (推荐使用)
CMD ["<param1>","<param2>",...]  # 该写法是为 ENTRYPOINT 指令指定的程序提供默认参数

# 类似于 CMD 指令，但其不会被 docker run 的命令行参数指定的指令所覆盖，而且这些命令行参数会被当作参数送给 ENTRYPOINT 指令指定的程序。
# 但是, 如果运行 docker run 时使用了 --entrypoint 选项，将覆盖 ENTRYPOINT 指令指定的程序。
ENTRYPOINT ["<executeable>","<param1>","<param2>",...]
# 比如
FROM nginx
ENTRYPOINT ["nginx", "-c"] # 定参
CMD ["/etc/nginx/nginx.conf"] # 变参 

# 设置环境变量，定义了环境变量，那么在后续的指令中，就可以使用这个环境变量
ENV <key> <value>
ENV <key1>=<value1> <key2>=<value2>...

# 构建参数，与 ENV 作用一致。不过作用域不一样。
# ARG 设置的环境变量仅对 Dockerfile 内有效，也就是说只有 docker build 的过程中有效，构建好的镜像内不存在此环境变量
ARG <参数名>[=<默认值>]

# 定义匿名数据卷。在启动容器时忘记挂载数据卷，会自动挂载到匿名卷
# 避免重要的数据，因容器重启而丢失，这是非常致命的。
# 避免容器不断变大。
VOLUME ["<路径1>", "<路径2>"...]
VOLUME <路径>
# 也可以通过启动容器时 -v 指定

# 用来给镜像添加一些元数据（metadata），以键值对的形式，语法格式如下
LABEL <key>=<value> <key>=<value> <key>=<value> ...
```


## Valgrind 内存泄露分析工具
```Shell
# 安装
sudo apt-get install valgrind

# 内存泄露检查
valgrind --leak-check=full ./my_program

```

## Perf 性能分析工具
```Shell
# 安装, 通常与 Linux 内核一起打包。你可以使用包管理器来安装 Perf
sudo apt-get install linux-tools-common linux-tools-generic

# CPU 性能分析(包括指令执行数、CPU 周期、缓存命中率等)
perf stat ./my_program

# 函数级性能分析
# 运行 my_program 并记录下所有的事件，然后可以使用 perf report 命令来生成函数级别的性能分析报告
perf record ./my_program
perf report
```


## Wireshark tcpdump 网络分析工具
```Shell
# 捕获指定网络接口上的所有数据包
sudo tcpdump -i <interface>
# 其中 <interface> 是要捕获的网络接口，如 eth0、wlan0
# 捕获指定 IP 地址的数据包
sudo tcpdump -i <interface> host <ip_address>
# 捕获指定端口的数据包
sudo tcpdump -i <interface> port <port_number>

# 使用 -w 参数可以将捕获的数据包保存到文件中，以便后续分析
sudo tcpdump -i <interface> -w capture.pcap

# capture.pcap 可以用 Wireshark 打开
```
