---
title:      linux多机通信
categories:
  - 工具
---

linux多机通信的一些方法记录。

## 使用nfs挂载网盘

使用nfs挂载网盘主要用于多机文件的共享。启动nfs服务的server也可以看作是一个FILE SERVER,用于在UNIX类系统之间共享文件，可以轻松的挂载(mount)到一个目录上，操作起来就像本地文件一样的方便。

### 服务器端配置

假设系统都为centos，检查nfs是否安装:`rpm -qa | grep nfs`。如果未安装，则进行手动安装。

    yum install -y nfs-utils
    yum install -y portmap
    service nfs start|stop|restart //服务端启动程序
    //如果遇到rpc错误，需要先启动rpcbind
    service rpcbind restart

### nfs配置

nfs服务的配置文件为/etc/exports。
exports文件的内容格式：<输出目录> [客户端1 选项（访问权限，用户映射，其他）] [客户端2 选项（访问权限，用户映射，其他）]，举个例子：

    /etc/exports      文件内容
    /tmp rz*(rw,async) 192.168.1.12(ro)
    /nfs/public 192.168.0.0/24(rw,async) *(ro)
    /home/frank 192.168.0.232(rw,sync)
    /nfs/root *.liusuping.com(ro,no_root_squash)
    /nfs/users *.liusuping.com(rw,insecure,all_squash,sync,no_wdelay)
    /mnt/cdrom 192.168.0.*(ro)
    /root/fs *(insecure,rw,async,no_root_squash)

输出目录：输出目录是指NFS系统中需要共享给客户机使用的目录；
客户端：客户端是指网络中可以访问这个NFS输出目录的计算机，客户端常用的指定方式：

-   指定ip地址的主机 192.168.0.200
-   指定子网中的所有主机 192.168.0.0/24
-   指定域名的主机 a.liusuping.com
-   指定域中的所有主机 *.liusuping.com
-   所有主机 *

选项：选项用来设置输出目录的访问权限、用户映射等。NFS主要有3类选项：

-   访问权限选项
-   设置输出目录只读 ro
-   设置输出目录读写 rw

用户映射选项：

-   all_squash 将远程访问的所有普通用户及所属组都映射为匿名用户或用户组（nfsnobody）；
-   no_all_squash 与all_squash取反（默认设置）；
-   root_squash 将root用户及所属组都映射为匿名用户或用户组（默认设置）;
-   no_root_squash 与rootsquash取反；
-   anonuid=xxx 将远程访问的所有用户都映射为匿名用户，并指定该用户为本地用户（UID=xxx）；
-   anongid=xxx 将远程访问的所有用户组都映射为匿名用 户组账户，并指定该匿名用户组账户为本地用户组账户（GID=xxx）；

其它选项

-   secure 限制客户端只能从小于1024的tcp/ip端口连接nfs服务器（默认设置）；
-   insecure 允许客户端从大于1024的tcp/ip端口连接服务器；
-   sync 将数据同步写入内存缓冲区与磁盘中，效率低，但可以保证数据的一致性；
-   async 将数据先保存在内存缓冲区中，必要时才写入磁盘；
-   wdelay 检查是否有相关的写操作，如果有则将这些写操作 一起执行，这样可以提高效率（默认设置）；
-   no_wdelay 若有写操作则立即执行，应与sync配合使用；
-   subtree 若输出目录是一个子目录，则nfs服务器将检查其父目录的权限(默认设置)；
-   no_subtree 即使输出目录是一个子目录,nfs服务器也不检查其父目录的权限,这样可以提高效率;

### 客户端配置

客户端配置相对简单，需要安装nfs客户端。安装完成以后执行以下命令。

    mkdir fs  #创建安装目录
    mount -t nfs xxx.xxx.xx.xx:服务端共享的目录路径名 fs #xxx指定机器名或者ip

## scp的免密登录

设置ssh的免密登录，主要是为了在使用ssh或scp等命令时无需输入密码，尤其在用脚本实现日常工作的自动化时显得非常重要。

### 使用ssh-keygen生成登录秘钥

ssh-keygen 是一个用来生成、创建和管理 SSH 认证用的公私钥的工具。进行登录的机器使用该命令生成公私钥。保存在用户家目录下的.ssh下面。

### 复制公钥

我们需要将ssh-keygen生成的公钥（如id_rsa.pub）放在被登录的机器下的.ssh/authorized_keys文件中，顺序添加即可。也可以使用ssh-copy-id命令。

### 可能遇到的问题

被登录的主机用户目录权限的问题：

-   假设是B主机，且免密登录用户名为test，test家目录权限需为700
-   .ssh目录权限需为700
-   authorized_keys文件权限需为600
