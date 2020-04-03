
# linux操作

cd // 进入某个文件  
cd ~ // 进入根目录  
cd .. // 进入上一级  
左键复制 右键黏贴  
cp [options] source... directory  
-a：此选项通常在复制目录时使用，它保留链接、文件属性，并复制目录下的所有内容。其作用等于dpR参数组合。
-d：复制时保留链接。这里所说的链接相当于Windows系统中的快捷方式。
-f：覆盖已经存在的目标文件而不给出提示。
-i：与-f选项相反，在覆盖目标文件之前给出提示，要求用户确认是否覆盖，回答"y"时目标文件将被覆盖。
-p：除复制文件的内容外，还把修改时间和访问权限也复制到新文件中。
-r：若给出的源文件是一个目录文件，此时将复制该目录下所有的子目录和文件。
-l：不复制文件，只是生成链接文件。
mv file1 file2
把当前目录下的file1文件名改成file2，如果该目录下有file2，则覆盖以前的file2文件
mkdir [-p] 目录名 // 创建新文件  
说明:  
参数：-p 确保目录名称存在，如果目录不存在的就新创建一个  

$ rm -rf 文件夹名字 // 删除某个文件夹  
说明：  
-r 向下递归，不管有多少级目录，一并删除  
-f 直接强行删除，不作任何提示的意思  
sudo是linux系统管理指令，是允许系统管理员让普通用户执行一些或者全部的root命令的一个工具，如halt，reboot，su等等  
如果需要管理员权限，则在前面增肌sudo即sudo rm -rf 文件夹的名字  

vim 文件名 // 打开某个文件的编辑界面
insert // 键位，进入输入状态
esc q! // 不保存退出
esc wq // 保存退出

## java部署相关

查看端口占用情况 lsof -i:端口号

查看Java进程获取pid号：

ps -ef|grep java|grep -v grep

部署Javajar包并指定输出日志文件（null不输出）：

nohup java -jar xx.jar >/dev/null &

&是必须的

## CentOS7通过yum安装JDK1.8

Yum（全称为 Yellow dog Updater, Modified）是一个在Fedora和RedHat以及CentOS中的Shell前端软件包管理器。基于RPM包管理，能够从指定的服务器自动下载RPM包并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软件包，无须繁琐地一次次下载、安装。

安装之前先检查一下系统有没有自带open-jdk

命令：

rpm -qa |grep java

rpm -qa |grep jdk

rpm -qa |grep gcj

如果没有输入信息表示没有安装。

如果安装可以使用rpm -qa | grep java | xargs rpm -e --nodeps 批量卸载所有带有Java的文件  这句命令的关键字是java

首先检索包含java的列表

yum list java*

检索1.8的列表

yum list java-1.8*

安装1.8.0的所有文件

yum install java-1.8.0-openjdk* -y

使用命令检查是否安装成功

java -version

到此安装结束了。这样安装有一个好处就是不需要对path进行设置，自动就设置好了
