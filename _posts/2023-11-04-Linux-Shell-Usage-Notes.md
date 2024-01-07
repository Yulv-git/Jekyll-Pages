---
layout: post
title: Linux Shell Usage Notes | Linux Shell 命令使用笔记
tags: Linux Shell
---

PS：本人记录的一些 Linux Shell 命令使用笔记

## 1. 用户、权限管理

### 1.1. 创建用户

``` bash
# 创建用户
sudo useradd -r -m -s /bin/bsah 用户名
# 设置密码
sudo passwd 用户名

# 指定目录下创建用户
sudo useradd -d /data/home/test -r -m -s /bin/bash test
# 或者：sudo useradd -d /home/test -m test
```

### 1.2. 服务器用户登录

``` bash
# ssh登录
ssh 用户名@ip

# vocode免密登录
ssh-keygen -t rsa -b 4096  # 本地CMD，运行下面命令，生成本地配置文件（若已生成，则用原来生成的）
scp 本地配置路径/.ssh/id_rsa.pub 用户名@ip地址:~/tmp.pub  # 将生成的秘钥（id_rsa.pub），传输到远程服务器
ssh 用户名@ip地址 "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat ~/tmp.pub >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys && rm -f ~/tmp.pub"  # 将秘钥写到远程服务器的ssh配置文件中
# 若因.ssh文件权限改变而无法免密登录，可执行如下命名：
chmod 700 /用户主目录/
chmod 700 /用户主目录/.ssh/
```

### 1.3. 权限管理

``` bash
# 把某个文件夹及其内容的权限授权（包括所属权限）给指定用户
sudo chown -R 用户名 文件夹
sudo chown -R 用户名:组 文件夹  # 同时指定用户组

# 给指定用户开通某个文件夹的权限，不包括所属权限
setfacl -R -m u:用户名:rwx 文件夹

# 给用户主目录设置权限，使得只有该用户才能访问
sudo chmod -R 700 用户主目录

# 设置共享目录，并赋予读写权限
chmod -R 777 /sharedata

# 设置用户所属的群组
sudo usermod -g G1 ahhh  # 给将用户ahhh的群组设置为G1
sudo usermod -G G2 ahhh  # 此外，再给ahhh设置为另一个群组G2的成员

# 给test用户赋予管理员的权限
sudo adduser test sudo
```

## 2. 环境配置

### 2.1. python虚拟环境

``` bash
# 创建
conda create -n 环境名 python=X.X
# 创建并安装库
conda create -n 环境名 numpy matplotlib python=X.X

# 激活
conda activate 环境名

# 查看已存在的虚拟环境
conda info -e  # 或 conda env list  # conda info --envs

# 删除
conda remove -n 环境名 --all
```

### 2.2. conda 镜像源

``` bash
# 添加conda清华镜像源
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/

# 换回conda的默认镜像源
conda config --remove-key channels
```

### 2.3. pip 镜像源

直接在~/.pip/pip.conf（若不存在，则新建该文件）中配置一下信息：

``` txt
[global]
index-url = https://pypi.douban.com/simple
[install]
trusted-host = https://pypi.douban.com
```

或者在安装时

``` bash
# 安装库时，指定镜像源
pip install -i  https://mirrors.aliyun.com/pypi/simple/ numpy

# 清华：https://pypi.tuna.tsinghua.edu.cn/simple
# 阿里云：http://mirrors.aliyun.com/pypi/simple/
# 中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/
# 华中理工大学：http://pypi.hustunique.com/
# 山东理工大学：http://pypi.sdutlinux.org/
# 豆瓣：http://pypi.douban.com/simple/

# note：新版ubuntu要求使用https源
```

### 2.4. 修改cuda版本

``` bash
ls /usr/local  # 查看现有的cuda版本，系统会默认使用最新的cuda版本
vim ~/.bashrc  # 打开配置文件，并在最后添加如下内容：
export PATH=/usr/local/cuda-10.1/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda-10.1/lib64:$LD_LIBRARY_PATH  # 其中，10.1则为要设置的cuda版本
# 重启当前终端，在新的终端中即可使用更改后的cuda
```

### 2.5. 创建快捷命令

``` bash
vim ~/.bashrc  # 打开配置文档最后输入:
alias ns=nvidia-smi

# 保存后，激活配置   
source ~/.bashrc
# 即可使用 ns 快捷命令
```

### 2.6. 系统变量

```bash
env  # 查看所有系统变量
```

### 2.7. 远程-本地，端口映射

``` bash
# 将远端的地址的端口8888，映射到本地的端口8888
ssh -L:8888:localhost:8888 usrname@ip
```

## 3. 系统命令

### 3.1. cpu

``` bash
# 总逻辑CPU数 = 物理CPU个数 * 每颗物理CPU的核数 * 超线程数
# 物理CPU个数
cat /proc/cpuinfo| grep "physical id"| sort| uniq| wc -l
# 每颗物理CPU的核数
cat /proc/cpuinfo| grep "cpu cores"| uniq
# 逻辑CPU总数
cat /proc/cpuinfo| grep "processor"| wc -l
# CPU型号
cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c
```

### 3.2. 显卡

``` bash
# 查看显卡使用情况
nvidia-smi

# 如果有几秒钟的延时，可管理员运行
sudo nvidia-persistenced --persistence-mode
# 或者，下面的命令也可以提升nvidia-smi运行速度
sudo /usr/bin/nvidia-persistenced --verbose

# 查看0号、1号显卡的 当前温度、关机温度、减速温度
nvidia-smi -q -i 0,1 -d TEMPERATURE

# 实时刷新并高亮显示状态
watch -n 1 -d nvidia-smi # 或用Python库查看：watch -n 1 -d gpustat -cpu
```

### 3.3. 操作系统版本信息

```bash
cat /proc/version  # Linux查看当前操作系统版本信息
```

### 3.4. ip

#### 3.4.1. 查看ip

``` bash
# 查看服务器ip
ip addr  # inet 便是ip地址
ifconfig  # inet 便是ip地址
```

#### 3.4.2. 修改ip

``` bash
ifconfig eth0 192.168.12.22  # 重启后无效

vim /etc/sysconfig/network-scripts/ifcfg-eth0  # 重启后永久生效
```

### 3.5. 用户

``` bash
# 查询当前登录的用户
who

# 查询当前登录的用户，及其相关信息
w
```

### 3.6. 进程

``` bash
# 查看
top  # 当前所有进程，类似windows的任务管理器
fuser /dev/nvidia*  # 查看当前用户在某张显卡上运行的进程
sudo fuser -v /dev/nvidia*  # 查看所有用户在某张显卡上运行的进程

# 查询某个PID是哪个用户的
cd /proc/PID  # 进入对应进程的路径，输入 ll 即可查询该路径文件的所有者，即该PID所属用户

# 关掉
kill PID
# 强制关掉
kill -9 PID

# 杀死指定用户所有进程
kill -9 $(ps -ef | grep hnlinux)  # 方法一 过滤出hnlinux用户进程 
kill -u hnlinux  # 方法二

# 查看僵尸进程
ps -A -ostat,ppid,pid,cmd | grep -e '^[zZ]'
# 杀死僵尸进程（上面得到的PID中的第一个为父进程，杀死父进程即可）
kill -HUP ppid
```

### 3.7. 内存空间

``` bash
# 查看CPU空余
free -h

# 查看磁盘内存空间
df -h
```

### 3.8. 开机自启

- 编写需要开机自启的sh脚本，或者添加到适当的现有sh脚本中。

例如 autorunFFF.sh：

``` bash
cd /home/XXX/FFF

nohup java -jar FFF.jar -start & echo $! 
```

- 设置脚本权限

``` bash
chmod 755 autorunFFF.sh
```

- 将脚本移动到/etc/init.d目录下

``` bash
sudo mv autorunFFF.sh /etc/init.d/
```

- 更新脚本优先级

``` bash
cd /etc/init.d/
sudo update-rc.d autorunFFF.sh defaults 90
```

- 若要取消该开机自启脚本程序，则：

``` bash
cd /etc/init.d/
sudo update-rc.d -f autorunFFF.sh remove
```

## 4. 字符匹配

### 4.1. 通配符

|      ---      |                                                           ---                                                           |
| :-----------: | :---------------------------------------------------------------------------------------------------------------------: |
|    通配符     |                                                          含义                                                           |
|       *       |                                                文件代表文件名中所有字符                                                 |
|    ls te*     |                                                  查找以 te 开头的文件                                                   |
|   ls *html    |                                                 查找结尾为 html 的文件                                                  |
|      ？       |                                                代表文件名中任意一个字符                                                 |
|    ls ?.c     |                                          只找第一个字符任意，后缀为 .c 的文件                                           |
|    ls a.?     |                               只找只有 3 个字符，前 2 字符为 a. ，最后一个字符任意的文件                                |
| [] "[” 和 “]” |                         将字符组括起来，表示可以匹配字符组中的任意一个。“-” 用于表示字符范围。                          |
|     [abc]     |                                                匹配 a、b、c 中的任意一个                                                |
|     [a-f]     |                                           匹配从 a 到 f 范围内的任意一个字符                                            |
|   ls [a-f]*   |                                      找到从 a 到 f 范围内的任意一个字符开头的文件                                       |
|    ls a-f     |                              查找文件名为 a-f 的文件,当 “-” 处于方括号之外失去通配符的作用                              |
|       \       | 如果要使通配符作为普通字符使用，可以在其前面加上转义字符。“?” 和 “*” 处于方括号内时不用使用转义字符就失去通配符的作用。 |
|     ls *a     |                                                 查找文件名为 *a 的文件                                                  |

### 4.2. grep 全局正则表达式输出

grep (global regular expression print，全局正则表达式输出)，是一个搜索工具。

``` bash
# 例1 在文件中查找模式（单词）
grep ahhh /etc/passwd

# 例2 在多个文件中查找模式
grep ahhh /etc/passwd /etc/shadow /etc/gshadow

# 例3 使用-l参数列出包含指定模式的文件的文件名
grep -l ahhh /etc/passwd /etc/shadow /etc/fstab /etc/mtab

# 例4 使用-n参数，在文件中查找指定模式并显示匹配行的行号
grep -n ahhh /etc/passwd

# 例5 使用-v参数输出不包含指定模式的行
grep -v ahhh /etc/passwd  # 输出/etc/passwd文件中所有不含单词“ahhh”的行

# 例6 使用 ^ 符号输出所有以某指定模式开头的行
grep ^root /etc/passwd  # 输出/etc/passes文件中所有以“root”开头的行

# 例7 使用 $ 符号输出所有以指定模式结尾的行。
grep bash$ /etc/passwd  # 输出/etc/passwd文件中所有以“bash”结尾的行

# 例8 使用 -r 参数递归地查找特定模式
grep -r ahhh /etc/  # 递归的在/etc目录中查找“ahhh”单词

# 例9 使用 grep 查找文件中所有的空行
grep ^$ /etc/shadow

# 例10 使用 -i 参数（忽略字符的大小写）查找模式
grep -i ahhh /etc/passwd  # 在paswd文件中查找“ahhh”单词

# 例11 使用 -e 参数查找多个模式
grep -e "ahhh" -e "root" /etc/passwd  # 在一条grep命令中查找‘ahhh’和‘root’单词

# 例12 使用 -f 用文件指定待查找的模式
grep -f grep_pattern /etc/passwd  # 使用grep_pattern文件进行搜索

# 例13 使用 -c 参数计算模式匹配到的数量
grep -c -f grep_pattern /etc/passwd

# 例14 输出匹配指定模式行的前或者后面N行
grep -B 4 "games" /etc/passwd  # 使用-B参数输出匹配行的前4行

grep -A 4 "games" /etc/passwd  # 使用-A参数输出匹配行的后4行

grep -C 4 "games" /etc/passwd  # 使用-C参数输出匹配行的前后各4行
```

## 5. 文件管理

### 5.1. 文件大小

``` bash
# 查看当前目录下所有文件总共的内存占用大小
du -sh

# 查看当前目录下，所有文件夹的内存占用大小
du -h
# 查看当前目录下，各一级目录的内存占用大小
du -h --max-depth=1  # 1 表示第一级目录
```

### 5.2. 复制文件

``` bash
# 同一服务器下，复制文件夹及其内容
cp -r /**/***/文件夹名 /*/存放复制的文件夹的文件夹名

# 不同服务器之间，复制文件夹及其内容
scp -r root@ip:/**/***/文件夹名 /*/存放复制的文件夹的文件夹名
scp -P 端口号 -r root@服务器ip:/home/wwwroot/.* /Users/apple/Desktop/  # 指定端口号

# 复制时自动输入密码：
sshpass -p 密码 scp -r root@ip:/**/***/文件夹名 /*/存放复制的文件夹的文件夹名
```

### 5.3. 输出重定向命令

Linux 允许将命令执行结果重定向到一个文件，本应显示在终端上的内容保存到指定文件中。如：

``` bash
ls > test.txt   # test.txt 如果不存在，则创建，存在则覆盖其内容
ls > test.txt 2>&1  # 2>&1：将标准错误输出重定向到标准输出
```

注意： > 输出重定向会覆盖原来的内容， >> 输出重定向则会追加到文件的尾部。

### 5.4. 查找文件

#### 5.4.1. find

find 命令功能非常强大，通常用来在特定的目录下搜索符合条件的文件，也可以用来搜索特定用户属主的文件。

常用用法：

|                  ---                   |                  ---                   |
| :------------------------------------: | :------------------------------------: |
|                  命令                  |                  含义                  |
|         find ./ -name test.sh          |  查找当前目录下所有名为test.sh的文件   |
|          find ./ -name '*.sh'          |   查找当前目录下所有后缀为.sh的文件    |
|         find ./ -name "[A-Z]*"         | 查找当前目录下所有以大写字母开头的文件 |
|           find /tmp -size 2M           |     查找在/tmp 目录下等于2M的文件      |
|          find /tmp -size +2M           |     查找在/tmp 目录下大于2M的文件      |
|          find /tmp -size -2M           |     查找在/tmp 目录下小于2M的文件      |
|      find ./ -size +4k -size -5M       |   查找当前目录下大于4k，小于5M的文件   |
|           find ./ -perm 0777           | 查找当前目录下权限为 777 的文件或目录  |
| find / -name "hadooop*" -exec rm {} \; |               查找并删除               |

### 5.5. 创建文件

``` bash
touch test.out  # 创建单个文件

touch {test1.log, cache2.txt}  # 同时创建两个文件
```

## 6. 历史命令

``` bash
history  # 显示命令历史

Ctrl+r  # 搜索历史命令
```
