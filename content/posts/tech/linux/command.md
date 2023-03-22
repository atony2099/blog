---
title: command
date: "2021-03-14T15:26:48+0800"
categories: ["linux"]
lastmod: 2023-03-22T13:54:16+0800
draft: false
---

## basic


1. backslash 1
   for shell command, \(newline) represent a same line

2. vairables with curly brackets
   variable expansion \${}

   1. distinguish it from other string: echo "\${foo}name"
   2. excute some script:
      get array `${array[1]}` replace variables: `${var/de/12}`;

3. curly braces
   \$(): command expansion



## text process

awk vs sed: text process

differ:
1. sed:  
2. awk: more  powerful 

###  awk

1. print  match

```
awk /rpc/ a.txt 
```



3. print  some colume 
```

data.txt
a b
a1 b1

awk  '{print  $1}' data.text
a
a1
```


2. find some columme is   equal to a value
```shell


cat a.txt
A    B    C
Tarun    A12    1
Man    B6    2

awk '{ if($2 == "B6") print $0;}'

Man B6 2 
```


3. get total count:  if  ... do

```
awak '/aaa/{count++} END {print count}' a.txt
```


###  sed

1. print the match
```
sed -n '/rpc/p' a.txt
```

2. replace   or delete  text 

```shell 
sed 's/foo/boo/g' data.txt

sed '/^\s*$/d' log.txt  # delete blank line

```




### wc


word, line, character, and byte count

-l: line
-w: worods
-c: characters


## source

read and excute commands from the file

### use case

define some global variable

```shell
foo='go' // a.xt
```

source a.txt

## file manager

### 1. inspecg

1. du: display directory usage
   1. -s(summarize): total for the direcotry
   2. -a: all file size

  ```
  du -h #current directory and subdirecotries size
  du -s # the current directory size
  ```

### create:

1. mkdir  
   1. -p: create intermediate directories as required


### update
1. mv

### del;
1. rm
   1. -r --recursive: remove the directories and their contents
   2. -f --force: 
      1. ignore prompt(whether to remove the file)
         1. a file is unwritable;stdin is a terminal,
      2. not show any error message if the file not exis;


### mv

mv source target
rename: if target isn't exist;

move: target is a directory and exist;

### rm 

1. -f --force: force without prompt,prompt when
   1. a file is write protected
      ```
      chmod 444 a.txt; rm a.txt
      ```
   
2. -r --recursive: remove  directory and their contents recursively





## http

### curl

create request

```shell
curl -X POST  \
-d  '{"hello":"world"}' \
-H 'Content-Type: application/json' \
http://baidu.com
```

```shell
curl -X POST \
-F 'image=@/home/user/Pictures/wallpaper.jpg' \
HTTP://baidu.com
```

## ps

report a snpashot of current process

## redirection

change the standinput(keyboard) standout(screen) to another object

1. three  open file:
   1. 0:
   2. 1: standard output(default in terminal )
   3. 2: standard error(default in terminal )


2. `>`:  overwrite file 
3. `>>`: append file 

4. case:
   ```   
   >/dev/null = 1>/dev/null
   2>&1: error 
   ```



### basic concept

1. EOF: represent start[end] of file;
2. single arror vs double array
   output:
   single: overwrite
   double: append
   input:
   single: for file
   double:

   ```
   cat << EOF
   ab
   EOF
   ```

3. change the output to a file
   `echo 'a' > a.txt`

4. change the input to a file

```
cat < a.txt
```

> : overwrite
>
> > : append

### 2

## how to create a command

1. create a shell script
2. move to l

## directory manage

### 1. find: walk a file hierarchy

-maxdepth:

-mindepth:

-name: file name

-type: f: file

### 2. cd

- : last dirctory

-

## download and archieve

1. wget:download
   1. -c: resume getting a partially-downladed file
   2. -O: the name of the downloaded file

2. tar: compress or exact file: tar xvfz

   ```shell
   tar -cvf a.tar file1 fil2
   tar -xvf a.tar

   ```

   1. -C: the target directory
   2. -f: the archive file
   3. -v: verbose
   4. -z: work only in c mode, compress the file with gzip

   5. mode:
      1. -c: compress
      2. -x: exact

## privilege

### chown

### umask

director:777 files:6666





## sync file


### rsync

`rsync source to dest`

argumetns:
1. -a: archive mode, which preserves file permissions, ownership, timestamps, etc.
	1. `-t`, `--times` preserve modification times
	2. `-g`, `--group` preserve group
	3. -o`, `--owner` preserve owner (super-user only)
2. -z: comprese during tranfering


what happen if uid not exist in des host:
the file will owed by souce host owner's id

```
a.txt  tang(501)

rsync -a  1.2.3.3:/

ls  1.2.3.3/
a owenr = 501 

```






example:

1. rsync - avz  /movies/*  /api :  sync all files to /api/,  /api/move1  /api/movie2
2.  rsync -avz /movies /api: sync directory and file to /api,  /api/movies/movie1....


3. parameters
   1. --exclude,--include: from left to right
   2. -a: preserve meta data;
      1. -r, --recursive recurse into directories
      2. -l, --links Copy symlinks as symlinks
      3. preserve
         1. -p, --perms Preserve permissions
         2. -t, --times Preserve times
         3. -g,-o, --Preserve group,user(only take effect in super user mode)
         4. --devices, This option causes rsync to transfer character and block device files to the remote system to recreate these devices.
         5. --specials: This option causes rsync to transfer special files such as named sockets and fifos.
   3. -c: compress during sync 
   4. --include 
   5. --exclude


4. case: sync a.txt to b 
   a/{c.txt,d.pdf, a1/a.txt}
   b
   ```
   sync a/ b --include *.txt --inclue='*.txt' --include='*/' --exclude='*' -avc 
   ```



rsync /mnt/tang01/qbitdownload/movie/*  /mnt/tang01/movies/movie  \'  --exclude=\'*\' -avc --chmod=a=rwx'



## kill

pkill 

1. pkil processname:  only partly match
```
pkill ginx, nginx will be ill
```
2. pkill  -x: full match