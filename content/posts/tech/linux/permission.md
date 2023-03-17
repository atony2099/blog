---
title: permission
date: "2022-06-03T17:19:04+0800"
draft: false
tags: ["linux","shell"]
---


## user
### su: 
1. what
   1. change user
   2. excute a command as another

2.  how
   1. -, -l: simulate a full login 

   2. -c: excute command



## user - group 
1. file belong to a group which the owner doesnt belong to  
   yes: share the data to other groups
   /public.html owner: root, group: apache,www




## inspect permission



## permission list
excute permission for directory
1. read:  
	1. read list: ls 
2. write:  delte/update/add the items(must enter the item first)
3. excute: 
      1. cd 
      2. read the meta of item: ls -l 
         ```
         01/01.txt
         ls -l 01
         ls: cannot access '01/01.txt': Permission denied
         total 0
         -????????? ? ? ? ?            ? 01.txt

         ```

   >  /var/root:  rwx --- ---;


2. permiss list 
   1. noraml
      1. read:+r 
      2. write:+w
      3. excute:x

   2. supplement,special permission bits
      1. sticky:+t, only the owner can rename/delete the file
      2. setuid,setgid: +s, grant permission temporarily
         ```
         -rwsr-xr-x root root /usr/bin/passwd
         ```   
         other user run passwd as root 



## change permission

### umask

1. what?
   change the file permissions 

2. when
   1. change new filles: processs(toucch,mkdir).
   2. chmod with symbol


3. how:
   1. file: 666-unmask(022): user:rw, group/other: r 
   2. directory: 777-unmask(022) user:rwx  ,group/other:rx;

### chmod
1. what
   change the permission of the files

2. types 
   1. number: chmod 777 a
   2. symbol:(will delete the maskvalue)
      1. chmod ugo=rwx,a=rwx;
      2. chmod u=rwx,g=rwx,o+r