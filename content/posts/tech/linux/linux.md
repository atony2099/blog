---
title: linux 
date: "2021-03-14T15:15:44+0800"
draft: false
categories: ["linux"]
---

[Filesystem Hierarchy Standard](https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard)

[What is the difference between Unix, Linux, BSD and GNU? ](https://unix.stackexchange.com/questions/104714/what-is-the-difference-between-unix-linux-bsd-and-gnu)

[Linux Boot Process](http://www.spiceupyourknowledge.net/2015/08/linux-boot-process.html)

[The BIOS/MBR Boot Process](https://neosmart.net/wiki/mbr-boot-process/)

[Master boot record](https://en.wikipedia.org/wiki/Master_boot_record)

[科普贴：BIOS和UEFI的启动项](https://zhuanlan.zhihu.com/p/31365115)

## linux architecture

![gItjx4](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210706/gItjx4.jpg)

cpu,ram,disk, device, network;


## 1. the process of booting

### legacy
![nPzwxP](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220528/nPzwxP.jpg)


1. feature: 不知道分区和文件，只能固定读取启动盘的第一个扇区；

2. stage
   1. stage 1: 通过table 知道分区
   2. stage 2: 通过引导程序 知道文件系统；；

3. BIOS:  basic input outpt system
   1. check haredware:Power On Self Test (post)
   2. load and excute  bootloader  in  MRB following the boot sequeuce




4. mbr/GPT: master boot recorder,stage 1 
   
   > 寻找bootable(active) partition
   1.  bootloader code;
   2.  partition list 

5. volume boot record: 真正的引导程序，stage 2,加载os
   1. config
      ```shell
       #boot=/dev/sda
      default=0
      timeout=5
      splashimage=(hd0,0)/boot/grub/splash.xpm.gz
      hiddenmenu
      title CentOS (2.6.18-194.el5PAE)
            root (hd0,0)
            kernel /boot/vmlinuz-2.6.18-194.el5PAE ro root=LABEL=/
            initrd /boot/initrd-2.6.18-194.el5PAE.img
      ```
   2. list:
      1. grub: linux
      2. 
6. kernel
   1. start up
   2. run init program:
      init programe read


### uefi 
![6EeBrF](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220528/6EeBrF.jpg)
1. feature:
   1. 是一个完整的程序，知道各个分区和文件系统，不需要额外中介，直接读取引导程序；


2. how to boot
   1. 文件启动: 指定明确目录，window manager --> 在 esp partition目录: /EFI/microsoft/boot/bootmgfw.efi 
   2. 设备启动(u盘): 为指定明确目录 boot from usb ，遍历分区,寻找/efi/boot/bootx64.efi



##  Run level


| ID     | Name                            | Description                                                                 |
| :----- | :------------------------------ | :-------------------------------------------------------------------------- |
| 0  Off | Turns off the device.           |                                                                             |
| 1      | Single-user mode                | Mode for administrative tasks.                                              |
| 2      | Multi-user mode                 | Does not configure network interfaces and does not export networks services |
| 3      | Multi-user mode with networking | Starts the system normally.                                                 |
| 4      | Not used/user-definable         | For special purposes.                                                       |
| 5      | Full mode                       | Same as runlevel 3 + display manager.                                       |
| 6      | Reboot                          | Reboots the device.                                                         |

##  init program

the first program start after the kernel booting, it is the ancestor of all other programs

1. SysV-style

1. systemed

## 1. linux base structure

![](http://ww1.sinaimg.cn/large/006dizvAly1g0glibj0xzj30a30870t4.jpg)

![](https://i1.wp.com/blog4jimmy.com/wp-content/uploads/2017/12/mmap2.png)

### 1. linux distribution constitue components

Kernel + gnu(a set of programs) + windows(optional)

### 2. system famliy

POSIX: portable operateing system interface

![T6qSnA](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210406/T6qSnA.jpg)

1. Bsd and unix are old implementation
2. linux is new comer

## directory  

![KA5qjy](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210320/KA5qjy.jpg)
![UhvrPG](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20220524/UhvrPG.jpg)


1. /home(\$HOME | ~):
   it's the workspace of a particular user;each user have its own individual files
2. /bin: fundamental user program
      >  cat, cp
3. /sbin:fundamental system program(run by root)
       >  init, route
4. /etc: configure files
5. /usr: contain the majority of user utilities and applications;
   1. /usr/bin: common program
   2. /user/sbin: common system program
   3. /usr/local/: local program
      > go,python...
6. /run: runtime variables
7. /tmp: maybe delete at any times
8. /lib: dynamic library for /bin and /sbin
9.  /opt
   optianl application software packagee
11. /var
    variables data; continually change during normal operation of the system
    1. /var/run: runtime variables
       > /var/run/mysqld/mysqld.pid
    2. /var/log
8. /dev:
   device files： disk,cpu,etc.

### 1. difference between

/opt vs /usr/local? 1. /usr/local
you should mimic the linux hierarchy, seperate your application into different folder
/usr/local/bin
/usr/local/lib

        /usr/local/etc

    2. /opt: don't have these restrictions
      > e.g.our applications





### bin  sbin 

sbin: used for system administrator
bin: used for normal user  

/bin, /sbin: essential command
1.  /bin: ls, cat
2.  /sbin:  iptables, ip 


/usr/bin  /usr/bin: distribution-manager; not essential  

1. /usr/bin: awk, sed 
2. /usr/sbin: httpd ， 


/user/local/bin /user/loca/sbin : local-user manager 

1. /user/local/bin:  go  






## root user 

1. what?
   hava the high privledge;

2. change user: su
   change to a substitute user,if user is not given, root is asumed
   1. -,  -l:  make the shell a login shell



3. /etc/sudoers visudo
   grant the user root priviliages

   formt:
   user/%group ALL=(ALL) NOPASSWD: command

               [run as user/group ]

   ```shell
      sys ALL=(ALL) NOPASSWD: ALL # user have root
      %admin    ALL = (ALL) ALL # group have root priviledge

      rd ALL=(ALL) NOPASSWD: /bin/whoami,/bin/cat,/bin/tail  #rd can excute these commnad as root user
   ```

4. grant the user root priviledge

   1. add to group: the wheel group
      %wheel ALL=(ALL): ALL
      usermod  -aG  wheel(sudo) rd

   2. grant to the root privilege directly
      sys ALL=(ALL) NOPASSWD: ALL





## manager user

/etc/passwd: user info
/etc/shaowd: password;;
/etc/group:

vipw：edit /etc/passwd safely

1. query
   less /etc/passwd

```shell
sys:x:1000:100:system Adminstrator:/home/sys:/bin/bash
# name userID groupID   userid info   (home directory)  login shell

groups root; # show groups;
id  root; # show id and group;

wheel:x:10:tang,rd
# name groupID username
```

2. add user
   `useradd kafka`
   -p: password
   -m/M: create/notCreate home director

update:
1. change group
   ```shell
   usermod -aG wheel kafka
   ```

2.  update name group home directory
```shell
groupadd ostechnix
usermod -d /home/ostechnix -m -g ostechnix -l ostechnix vagrant
```


4. delete
   userdel username;
   1. -r: remove home directory and mail 

      



### group

1. primary group and secondary group
   when a new user is created `useradd`,  
   it will create a group which is same name with the user, and will be the primary group of the user

2. cat /etc/group
   `tang:x:1001`: groupname: password: groupID;

## linux signal list

1:hangup
2:interrupt(ctrl+c)
3:quit (ctrl+\)
9:kill: cant't be caught or ignored
15:terminate: software termination signal
18: stop signal generated from keyboard

> put a process in background,suspended; can be recoverd by fg command;

## job manage

job: a proces from current terminal

1. foreground: start process from current terminal
2. background:&
3. stopped: ctrl+z;
   bg: bring the job into the background
   fg: bring the job into the foreground
   jobs: list all jobs

## terminal tty console

terminal and console are both hardware which can interact with a host, basically a keyboard with a display;
in the early years, it's tty;





## i/o mutiple



## linux directory

/srv:  data files for a specific service  如 /srv/www/ftpfile,   /srv/indexhtml

/opt: 三方软件不是遵循linux的方式安装的，自己编译的； 


/srv 和 /opt都可以部署自己的服务；

如 goApp,  
 /srv/goApp: 此时goAPP二进制文件可以看做服务特别的数据




/var: 程序允许过程中产生的，如日志，cache； 


## 发行版本
RHEL: 
1. red hat 维护的或者与其相关的
2. 使用yum 安装包
3. 代表： rhel, fleodra, cento s

debian
1. 开源的
2. 使用apt安装
3. 代表： debian , ubuntu， 

Fedora-> centos stream -> rhel->centos
fedora 和stream 都是测试版本, centos 和 rhel都是稳定版本