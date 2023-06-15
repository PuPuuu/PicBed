## CLI

- `whatis` 用于查询一个命令执行什么功能
- `which`用于查找并显示给定命令的绝对路径，环境变量 PATH 中保存了查找命令时需要遍历的目录
- `whereis`定位指令的二进制程序、源代码文件和 man 手册页等相关文件的路径
- `man`Linux 下的帮助指令，通过 man 指令可以查看 Linux 中的指令帮助、配置文件帮助和编程帮助等信息

```
$ man date # 查看 date 命令的帮助手册
$ man 3 printf # 查看 printf 命令的帮助手册中的第 3 类
$ man -k keyword # 根据命令中部分关键字来查询命令
```

> man 页面的分类(常用的是分类 1 和分类 3)：
>
> 1. 可执行程序或 shell 命令
> 2. 系统调用(内核提供的函数)
> 3. 库调用(程序库中的函数)
> 4. 特殊文件(通常位于 /dev)
> 5. 文件格式和规范，如 /etc/passwd
> 6. 游戏
> 7. 杂项(包括宏包和规范，如 man(7)，groff(7))
> 8. 系统管理命令(通常只针对 root 用户)
> 9. 内核例程 [非标准]

- `free`

```
-b # 以Byte为单位显示内存使用情况；
-k # 以KB为单位显示内存使用情况；
-m # 以MB为单位显示内存使用情况；
-g # 以GB为单位显示内存使用情况。
-o # 不显示缓冲区调节列；
-s<间隔秒数> # 持续观察内存使用状况；
-t # 显示内存总和列；
-V # 显示版本信息。
```

- `grep`

```
-a --text  # 不要忽略二进制数据。
-A <显示行数>   --after-context=<显示行数>   # 除了显示符合范本样式的那一行之外，并显示该行之后的内容。
-b --byte-offset                           # 在显示符合范本样式的那一行之外，并显示该行之前的内容。
-B<显示行数>   --before-context=<显示行数>   # 除了显示符合样式的那一行之外，并显示该行之前的内容。
-c --count    # 计算符合范本样式的列数。
-C<显示行数> --context=<显示行数>或-<显示行数> # 除了显示符合范本样式的那一列之外，并显示该列之前后的内容。
-d<进行动作> --directories=<动作>  # 当指定要查找的是目录而非文件时，必须使用这项参数，否则grep命令将回报信息并停止动作。
-e<范本样式> --regexp=<范本样式>   # 指定字符串作为查找文件内容的范本样式。
-E --extended-regexp             # 将范本样式为延伸的普通表示法来使用，意味着使用能使用扩展正则表达式。
-f<范本文件> --file=<规则文件>     # 指定范本文件，其内容有一个或多个范本样式，让grep查找符合范本条件的文件内容，格式为每一列的范本样式。
-F --fixed-regexp   # 将范本样式视为固定字符串的列表。
-G --basic-regexp   # 将范本样式视为普通的表示法来使用。
-h --no-filename    # 在显示符合范本样式的那一列之前，不标示该列所属的文件名称。
-H --with-filename  # 在显示符合范本样式的那一列之前，标示该列的文件名称。
-i --ignore-case    # 忽略字符大小写的差别。
-l --file-with-matches   # 列出文件内容符合指定的范本样式的文件名称。
-L --files-without-match # 列出文件内容不符合指定的范本样式的文件名称。
-n --line-number         # 在显示符合范本样式的那一列之前，标示出该列的编号。
-P --perl-regexp         # PATTERN 是一个 Perl 正则表达式
-q --quiet或--silent     # 不显示任何信息。
-R/-r  --recursive       # 此参数的效果和指定“-d recurse”参数相同。
-s --no-messages  # 不显示错误信息。
-v --revert-match # 反转查找。
-V --version      # 显示版本信息。
-w --word-regexp  # 只显示全字符合的列。
-x --line-regexp  # 只显示全列符合的列。
-y # 此参数效果跟“-i”相同。
-o # 只输出文件中匹配到的部分。
-m <num> --max-count=<num> # 找到num行结果后停止查找，用来限制匹配行数
```

```
^    # 锚定行的开始 如：'^grep'匹配所有以grep开头的行。
$    # 锚定行的结束 如：'grep$' 匹配所有以grep结尾的行。
.    # 匹配一个非换行符的字符 如：'gr.p'匹配gr后接一个任意字符，然后是p。
*    # 匹配零个或多个先前字符 如：'*grep'匹配所有一个或多个空格后紧跟grep的行。
.*   # 一起用代表任意字符。
[]   # 匹配一个指定范围内的字符，如'[Gg]rep'匹配Grep和grep。
[^]  # 匹配一个不在指定范围内的字符，如：'[^A-FH-Z]rep'匹配不包含A-R和T-Z的一个字母开头，紧跟rep的行。
\(..\)  # 标记匹配字符，如'\(love\)'，love被标记为1。
\<      # 锚定单词的开始，如:'\<grep'匹配包含以grep开头的单词的行。
\>      # 锚定单词的结束，如'grep\>'匹配包含以grep结尾的单词的行。
x\{m\}  # 重复字符x，m次，如：'0\{5\}'匹配包含5个o的行。
x\{m,\}   # 重复字符x,至少m次，如：'o\{5,\}'匹配至少有5个o的行。
x\{m,n\}  # 重复字符x，至少m次，不多于n次，如：'o\{5,10\}'匹配5--10个o的行。
\w    # 匹配文字和数字字符，也就是[A-Za-z0-9]，如：'G\w*p'匹配以G后跟零个或多个文字或数字字符，然后是p。
\W    # \w的反置形式，匹配一个或多个非单词字符，如点号句号等。
\b    # 单词锁定符，如: '\bgrep\b'只匹配grep。
```

### Linux文件目录管理

#### 目录结构

![image-20230615134045506](/Users/yejunjie/hub/notes/assets/image-20230615134045506.png)

### 文件属性

```
$ ls -l
total 64
dr-xr-xr-x 2 root root 4096 Dec 14 2012 bin
dr-xr-xr-x 4 root root 4096 Apr 19 2012 boot

=======
当为 d 则是目录
当为 - 则是文件；
若是 l 则表示为链接文档(link file)；
若是 b 则表示为装置文件里面的可供储存的接口设备(可随机存取装置)；
若是 c 则表示为装置文件里面的串行端口设备，例如键盘、鼠标(一次性读取装置)。
```

![image-20230615135025335](/Users/yejunjie/hub/notes/assets/image-20230615135025335.png)

- 第 0 位确定文件类型
- 第 1-3 位确定属主（该文件的拥有者）拥有该文件的权限。
- 第 4-6 位确定属组（拥有者的同组用户）拥有该文件的权限。
- 第 7-9 位确定其他用户拥有该文件的权限。

### 文件管理

- 探测文件类型 - 使用 `file`
- 设置文件或目录的权限 - 使用 `chmod`
- 设置文件或目录的拥有者或所属群组 - 使用 `chown`
- 查找文件或目录 - 使用 `locate`
- 在指定目录下查找文件 - 使用 `find`

- 复制文件或目录 - 使用 `cp`
- 复制文件或目录到远程服务器 - 使用 `scp`
- 移动文件或目录 - 使用 `mv`
- 删除文件或目录 - 使用 `rm`

```
chmod 764 f01
chmod a+x f01　　    # 对文件f01的u,g,o都设置可执行属性

# 将目录/usr/meng及其下面的所有文件、子目录的文件主改成 liu
chown -R liu /usr/meng

# 拷贝文件到远程服务器的指定目录
scp <file> <user>@<ip>:<url>
scp test.txt root@192.168.0.1:/opt

# 拷贝目录到远程服务器的指定目录
scp -r <folder> <user>@<ip>:<url>
scp -r test root@192.168.0.1:/opt
```

####  Linux 文件压缩和解压

- 压缩和解压 tar 文件 - 使用 `tar`
- 压缩和解压 gz 文件 - 使用 `gzip`
- 压缩和解压 zip 文件 - 分别使用 `zip`、`unzip`

#### Linux网络管理

- 下载文件 - 使用 `curl`、`wget`
- telnet 方式登录远程主机，对远程主机进行管理 - 使用 `telnet`
- 查看或操纵 Linux 主机的路由、网络设备、策略路由和隧道 - 使用 `ip`
- 查看和设置系统的主机名 - 使用 `hostname`
- 查看和配置 Linux 内核中网络接口的网络参数 - 使用 `ifconfig`
- 查看和设置 Linux 内核中的网络路由表 - 使用 `route`
- ssh 方式连接远程主机 - 使用 `ssh`
- 为 ssh 生成、管理和转换认证密钥 - 使用 `ssh-keygen`
- 查看、设置防火墙（Centos7），使用 `firewalld`
- 查看、设置防火墙（Centos7 以前），使用 `iptables`
- 查看域名信息 - 使用 `host`, `nslookup`
- 设置路由 - 使用 `nc/netcat`
- 测试主机之间网络是否连通 - 使用 `ping`
- 追踪数据在网络上的传输时的全部路径 - 使用 `traceroute`
- 查看当前工作的端口信息 - 使用 `netstat`

```
$ ip link show                     # 查看网络接口信息
$ ip link set eth0 upi             # 开启网卡
$ ip link set eth0 down            # 关闭网卡
$ ip link set eth0 promisc on      # 开启网卡的混合模式
$ ip link set eth0 promisc offi    # 关闭网卡的混个模式
$ ip link set eth0 txqueuelen 1200 # 设置网卡队列长度
$ ip link set eth0 mtu 1400        # 设置网卡最大传输单元
$ ip addr show     # 查看网卡IP信息
$ ip addr add 192.168.0.1/24 dev eth0 # 设置eth0网卡IP地址192.168.0.1
$ ip addr del 192.168.0.1/24 dev eth0 # 删除eth0网卡IP地址

$ ip route show # 查看系统路由
$ ip route add default via 192.168.1.254   # 设置系统默认路由
$ ip route list                 # 查看路由信息
$ ip route add 192.168.4.0/24  via  192.168.0.254 dev eth0 # 设置192.168.4.0网段的网关为192.168.0.254,数据走eth0接口
$ ip route add default via  192.168.0.254  dev eth0        # 设置默认网关为192.168.0.254
$ ip route del 192.168.4.0/24   # 删除192.168.4.0网段的网关
$ ip route del default          # 删除默认路由
$ ip route delete 192.168.1.0/24 dev eth0 # 删除路由
```

#### Linux 系统管理

- 查看系统当前进程状态 - 使用`ps`
- 管理需要周期性执行的任务，使用`crontab`

```
# 每 1 分钟执行一次 command
* * * * * command

# 每小时的第 3 和第 15 分钟执行
3,15 * * * * command

# 在上午 8 点到 11 点的第 3 和第 15 分钟执行
3,15 8-11 * * * command

# 每隔两天的上午 8 点到 11 点的第 3 和第 15 分钟执行
3,15 8-11 */2 * * command

# 每个星期一的上午 8 点到 11 点的第 3 和第 15 分钟执行
3,15 8-11 * * 1 command

# 每晚的 21:30 重启 smb
30 21 * * * /etc/init.d/smb restart

# 每月 1、10、22 日的 4 : 45 重启 smb
45 4 1,10,22 * * /etc/init.d/smb restart

# 每周六、周日的 1:10 重启 smb
10 1 * * 6,0 /etc/init.d/smb restart

# 每天 18 : 00 至 23 : 00 之间每隔 30 分钟重启 smb
0,30 18-23 * * * /etc/init.d/smb restart

# 每星期六的晚上 11:00 pm 重启 smb
0 23 * * 6 /etc/init.d/smb restart

# 每一小时重启 smb
* */1 * * * /etc/init.d/smb restart

# 晚上 11 点到早上 7 点之间，每隔一小时重启 smb
* 23-7/1 * * * /etc/init.d/smb restart

# 每月的 4 号与每周一到周三的 11 点重启 smb
0 11 4 * mon-wed /etc/init.d/smb restart

# 一月一号的 4 点重启 smb
0 4 1 jan * /etc/init.d/smb restart

# 每小时执行`/etc/cron.hourly`目录内的脚本
01 * * * * root run-parts /etc/cron.hourly
```

### more

[参考](https://github.com/dunwu/linux-tutorial/blob/master/docs/linux/cli/%E5%91%BD%E4%BB%A4%E8%A1%8C%E7%9A%84%E8%89%BA%E6%9C%AF.md)