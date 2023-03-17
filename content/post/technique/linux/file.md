---
title: file
date: "2021-07-06T19:34:14+0800"
draft: false
tags: ["io"]
---

[图解 Linux 文件系统](https://juejin.cn/post/6969148449547616269)

[Nonblocking I/O](https://copyconstruct.medium.com/nonblocking-i-o-99948ad7c957)

[从内核文件系统看文件读写过程](https://www.cnblogs.com/huxiao-tee/p/4657851.html)

[files](https://man7.org/training/download/lusp_fileio_slides.pdf)

[图解|什么是缺页错误Page Fault](https://blog.csdn.net/g6U8W7p06dCO99fQ3/article/details/107240849)

[Processes and Tasks](http://ece-research.unm.edu/jimp/310/slides/os_essentials.html)

[INTRODUCTION TO THE LINUX VIRTUAL FILESYSTEM (VFS) -- PART I: A HIGH-LEVEL TOUR](https://www.starlab.io/blog/introduction-to-the-linux-virtual-filesystem-vfs-part-i-a-high-level-tour)

[Linux in Depth - 文件系统及 Socket 源码解析](http://jinke.me/2018-08-23-socket-and-linux-file-system/)

[Linux内核Page Cache和Buffer Cache关系及演化历史](https://lday.me/2019/09/09/0023_linux_page_cache_and_buffer_cache/)

[深度理解 Linux 读取文件过程！](https://zhuanlan.zhihu.com/p/371574406)

[原来8张图，就可以搞懂「零拷贝」了](https://www.cnblogs.com/xiaolincoding/p/13719610.html)

[Linux I/O 原理和 Zero-copy 技术全面揭秘](https://zhuanlan.zhihu.com/p/308054212)

[操作系统 I/O 全流程详解](https://www.cnblogs.com/cxuanBlog/p/13156493.html)


![Y3iPSE](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210808/Y3iPSE.jpg)
![Xohv5b](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210709/Xohv5b.jpg)
![GuXZrh](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210808/GuXZrh.jpg)
![S4d1Zl](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210806/S4d1Zl.jpg)
![EB72WD](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210812/EB72WD.jpg)






##  data structure

```c
openfile table:
file-> node

```




```c
// process
struct task_struct {
	// ...
	/* Filesystem information: */
	struct fs_struct		*fs;

	/* Open file information: */
	struct files_struct		*files;
	// ...
}

struct files_struct {
	// ...
	struct fdtable __rcu *fdt;
	// ...
}

struct fdtable {
	unsigned int max_fds;
	struct file __rcu **fd;      /* current fd array */
	unsigned long *close_on_exec;
	unsigned long *open_fds;
	unsigned long *full_fds_bits;
	struct rcu_head rcu;
};

struct file {
    struct path                     f_path;         // a dentry and a mount point which locate this file
    struct inode                    *f_inode;       // the inode underlying this file
    const struct file_operations    *f_op;          // callbacks to function which can operate on this file
    spinlock_t                      f_lock;
    atomic_long_t                   f_count;
    unsigned int                    f_flags;
    fmode_t                         f_mode;
    struct mutex                    f_pos_lock;
    loff_t                          f_pos           // offset in the file from which the next read or write shall commence
    struct fown_struct              f_owner;
    void                            *private_data
    struct address_space            *f_mapping;     // callbacks for memory mapping operations
}; 
```


```c
   struct dentry {
    struct hlist_bl_node            d_hash;                     // lookup hash list
    struct dentry                   *d_parent;	                // parent directory
    struct qstr                     d_name;
    struct inode                    *d_inode;                   // where the name belongs to
    unsigned char                   d_iname[DNAME_INLINE_LEN];  // small names
    const struct dentry_operations  *d_op;
    void                            *d_fsdata;                  // fs-specific data
    struct list_head                d_child;                    // child of parent list, i.e., our siblings
    struct list_head                d_subdirs;                  // our children
};


struct inode {
     __u16 i_mode;     // 模式
     __u16 i_nlinks;   // 链接数
     __u16 i_uid;      // 所属用户UID
     __u16 i_gid;      // 所属组ID
     __u32 i_size;     // 文件大小
     __u32 i_atime;    // 访问时间
     __u32 i_mtime;    // 修改时间
     __u32 i_ctime;    // 创建时间
     __u32 i_zone[10]; // 文件数对应的数据块编号
};

```




###  hard link and symbolic link
hard： newFile->node,   node link increate ,          origin file delte,  hard access the data 
soft:   newfile->originfile, node link not increase,  oriign file delete,   soft can't access the data 
1. hard link;
  1. create a new entry pointer to  nodes; increase inode's links
2. soft link 
  2. create a new node; the node save the entry of the pointed files



### disk vs sockets



1. 建立映射部分是一样的实现方式

task--> private file table---> file;

2. 实际的存储部分，也就是inode开始出现差异







## 是什么

![HkiTBY](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210816/HkiTBY.jpg)

为用户层提供i/o的统一接口；调用底层的实现(ext2,ext4)；

  1. char: 连续字节流；键盘/鼠标
  2. block: 被分成很多block；disk/usb;
  3. socket:网络
     

## 主要数据结构

1. task_struct.[files_struct].[fdtable]: openfile table;
2. fdtable[n]--> file: file;
3. file--> entry--> inode;


```c
// process
struct task_struct {
	// ...
	/* Filesystem information: */
	struct fs_struct		*fs;

	/* Open file information: */
	struct files_struct		*files;
	// ...
}

struct files_struct {
	// ...
	struct fdtable __rcu *fdt;
	// ...
}

struct fdtable {
	unsigned int max_fds;
	struct file __rcu **fd;      /* current fd array */
	unsigned long *close_on_exec;
	unsigned long *open_fds;
	unsigned long *full_fds_bits;
	struct rcu_head rcu;
};

struct file {
    struct path                     f_path;         // a dentry and a mount point which locate this file
    struct inode                    *f_inode;       // the inode underlying this file
    const struct file_operations    *f_op;          // callbacks to function which can operate on this file
    spinlock_t                      f_lock;
    atomic_long_t                   f_count;
    unsigned int                    f_flags;
    fmode_t                         f_mode;
    struct mutex                    f_pos_lock;
    loff_t                          f_pos           // offset in the file from which the next read or write shall commence
    struct fown_struct              f_owner;
    void                            *private_data
    struct address_space            *f_mapping;     // callbacks for memory mapping operations
}; 
```


```c
   struct dentry {
    struct hlist_bl_node            d_hash;                     // lookup hash list
    struct dentry                   *d_parent;	                // parent directory
    struct qstr                     d_name;
    struct inode                    *d_inode;                   // where the name belongs to
    unsigned char                   d_iname[DNAME_INLINE_LEN];  // small names
    const struct dentry_operations  *d_op;
    void                            *d_fsdata;                  // fs-specific data
    struct list_head                d_child;                    // child of parent list, i.e., our siblings
    struct list_head                d_subdirs;                  // our children
};


struct inode {
     __u16 i_mode;     // 模式
     __u16 i_nlinks;   // 链接数
     __u16 i_uid;      // 所属用户UID
     __u16 i_gid;      // 所属组ID
     __u32 i_size;     // 文件大小
     __u32 i_atime;    // 访问时间
     __u32 i_mtime;    // 修改时间
     __u32 i_ctime;    // 创建时间
     __u32 i_zone[10]; // 文件数对应的数据块编号
};

```




### disk vs sockets



1. 建立映射部分是一样的实现方式

task--> private file table---> file;

2. 实际的存储部分，也就是inode开始出现差异





## controller
![yxZpdy](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211118/yxZpdy.jpg)

cpu无法直接跟硬件直接交流，必须通过控制器 
cpu(or other controller:dma...)->controller(driver)-> device;

controller
   1. buffer data;
   2. excute command; 

## 文件读写过程

![3i70pG](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210928/3i70pG.jpg)
![W8s35D](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210928/W8s35D.jpg)



> 读写都是基于pageCache;

### 1. read 
KernelBuffer From OutSide; then userBuffer From KernelBuffer                                            

1. process 根据 descriptor table 找到对应innode;
2. innode 存储对应 pageCache;
  1. pageCache != null -->直接读取 pageCache
  2. pageCache =null 页缺失异常，创建pageCache，读取disk数据到pageCache;

3. copy pageCache to userCache


### write(删改查)

UserBuffer To Kernel Buffer
1. ..
2. ..
  1. pageCache != null 
  2. pageCache =null 页缺失异常，创建pageCache,读取disk数据到pageCache

3. 更新pageCache,pageCache被标记为dirty page


-----------write 返回；
> 此时如果断电，则刚写入会丢失!;
4. 写入disk;
   1. 定期写会disk
   2. 手动调用 sync() or  fsync()写入磁盘



#### write code 
the process:
open, write , fsync

code:
```c

#include <stdio.h>
#include <fcntl.h>

int main()
{
  char my_write_str[] = "1234567890";
  char my_read_str[100];
  char my_filename[] = "snazzyjazz.txt";
  int my_file_descriptor, close_err;

  /* Open the file.  Clobber it if it exists. */
  my_file_descriptor = open (my_filename, O_RDWR | O_CREAT | O_TRUNC);

  /* Write 10 bytes of data and make sure it's written */
  write (my_file_descriptor, (void *) my_write_str, 10);
  fsync (my_file_descriptor);
  close(my_file_descriptor);

}
```





## how to lookup block

![xXPb6p](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210709/xXPb6p.jpg)

data array index and composited array index

data[1~7] ---> block;
data[8][1-n]---> n block;
data[8][1-n][1-n]---> n^2 block






## how to add a new disk to current host

```shell
// 1.  create partition
 fdisk -l
 fdisk /dev/vdb

// 2. create a specific filSystem
 mkfs -t ext4 /dev/vdb1

// 3. mount (attach disk filesystem to linux file system)
  mount /dev/vdb1 /data
```

create a config for permanent mount at boot time

```
/etc/fstab
/dev/xvdc1 /data ext4 defaults     0   0
```



### command

1. fdisk: manipulate partition table
   1. fdisk -l: list the partition for all disk
2. df: report current file system usage






##  读写单位

sector(扇区):磁盘读写最小单位；
block--N个sector组成: 文件读写最小单位(内核层面;内核操作磁盘单位);

pageCache:  文件读写最小单位(用户层面；用户读写文件)；

![fxm4Z4](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210927/fxm4Z4.jpg)

![WRO02q](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210927/WRO02q.jpg)



##  hard disk  i/o

![vQpTiN](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/vQpTiN.jpg)


![GkXCXm](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/GkXCXm.jpg)

![1JEAN7](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210927/1JEAN7.jpg)

存储容量=磁头数*磁道（柱面）数*每道扇区数*每扇区字节数


### 1. 过程

1. 寻道: Tseek是指将读写磁头移动至正确的磁道上所需要的时间。
2. 旋转延迟: Trotation是指盘片旋转将请求数据所在的扇区移动到读写磁头下方所需要的时间
3. 传输:  Ttransfer是指完成传输所请求的数据所需要的时间

IOPS(input output per second) = 1000ms / (Tseek + Trotation + Transfer)

### i/o优化；
1. 顺序写入
2. ...


### 顺序写入(追加写)；

新增数据的时候直接写在现有数据地址后面；
1. case:
   wal, kafka log

2. 非顺序写入消耗:
   1. 定位地址；
   2. 磁盘消耗: 寻道+旋转

3. btree是吗
   不是,新增数据需要写入某个特定的page上

4. crons:
   读比较麻烦；


## DMA VS not DMA

### 1. not DMA;


![rwVLiS](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20211118/rwVLiS.jpg)

cpu copy data from disk controller to kernel buffer;

   1. controller register-> cpu register,
   2. cpu regsiter to kernel buffer 

### 2. DMA
![GtLvNV](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210928/GtLvNV.jpg)

dma controller  copy data from disk controller  


## zero copy

what:
reduce data copy times

for:
improve speed of  data transport 


complete copy:
```c
buffer = File.read 
Socket.send(buffer)
```

![4U8Cjp](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210928/4U8Cjp.jpg)


### how 

####  mmmp
```c
buf = mmap(file, len);
write(sockfd, buf, len);
```

![5cQ1Hv](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/5cQ1Hv.jpg)


#### sendfile
```c
#include <sys/socket.h>
ssize_t sendfile(int out_fd, int in_fd, off_t *offset, size_t count);
```

![50Xekq](https://raw.githubusercontent.com/atony2099/imgs/master/uPic/50Xekq.jpg)


## file share

1. nfs:a distributed file system protocol that allows you to share remote directories over a network. 
   ```
   mount -t nfs 10.10.0.10:/backups /var/backups
   ```
2. smb:The Server Message Block protocol (
3. ftp
4. webdav











