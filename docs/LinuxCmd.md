## 重要但不常用到的 linux 命令
```Shell
# 手动指定动态链接库的路径
export LD_LIBRARY_PATH=/path/to/your/library:$LD_LIBRARY_PATH

# 命令
top 
# PID（进程 ID）、USER（进程所属用户）、PR（进程的优先级）、NI（进程的 nice 值）
# VIRT（进程的虚拟内存使用量）、RES（进程的物理内存使用量）、SHR（进程的共享内存使用量）、
# S（进程的状态）、CPU（进程的 CPU 使用率）、%MEM（进程的内存使用率）、TIME+（进程的累计 CPU 使用时间）、COMMAND（进程的命令名称）

# 网络状态命令
netstat 
# 参数用法
# -a 显示所有连接
# -t 显示 tcp 连接
# -u 显示 udp 连接
# -l 显示监听中的连接
# -p 显示 PID 和程序名
# -n 以数字形式显示地址和端口号, 而不是尝试解析为主机名和服务名
# -r 显示系统的路由表, 包括地址和默认网关, 接口

# awk 命令
# 其中，pattern 是用于匹配文本行的模式，action 是在匹配到的行上执行的动作。file 是要处理的文本文件名。
awk 'pattern { action }' file
# 以下为一些常用用法

# 打印全部行
awk '{ print }' file 
# 打印文件中每一行的第一个和第二个字段
awk '{ print $1, $2 }' file
# 打印包含指定模式的行(pattern 是正则表达式)
awk '/pattern/ { print }' file
# 计算文件中第一个字段的总和，并在文件末尾打印结果
awk '{ sum += $1 } END { print sum }' file
# 使用逗号作为字段分隔符来处理文本文件
awk -F',' '{ print $1, $2 }' file

# df 磁盘命令
df -h [file_system]
# 例如
df -h # 显示系统中所有挂载的文件系统的磁盘使用情况
df -h /dev/nvme0n1p10 # 显示指定文件系统的使用情况
```