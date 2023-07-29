---
title: git
date: "2021-03-14T15:35:06+0800"
categories: ["git"]
lastmod: 2023-03-20T09:52:44+0800
draft: false
---

[The Most Basic Git Command List](https://www.tutorialdocs.com/article/git-basic-command-list.html)

[Git Internals - Git References](https://git-scm.com/book/en/v2/Git-Internals-Git-References)

[git-log](https://git-scm.com/docs/git-log)


## git overview

![OwXxW0Qt3mNC](https://cdn.jsdelivr.net/gh/toms2077/imgs@master/20230729/OwXxW0Qt3mNC.png)


![KJFkJw](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210331/KJFkJw.jpg)
![usyT2X](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210331/usyT2X.jpg)
![e6VPjC](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210331/e6VPjC.jpg)


what: 
version control , track the change of source code


## working/stage/repo 
working tree: 本地的草稿纸，可能有很多草稿纸
staging area: 已经完成的章节的复印件
local: 已经完成的章节并完成编辑部审核的复印件
remote:  完成审核后存储在银行保险箱复印件

why need stage area: 允许部分提交草稿


working tree is clean:    working stage and repo 完全同步







### reset


git reset:  reset commit to some one 

how it work:
1.  init:  wor: n, stage:n   commit: n
2. soft -1, unchange work and stag:   work n, stage: n,  commit: n-1
```shell
git status  
some file to be commitd
```


3. mixed -1: reset stage-1,  work n, stage n-1, commit n-1 
```
git status
change not staged ...
change not tack ...
```

4. hard -1: stege and work -1: work n-1, stage n-1, commit -1
	


### git clean

remove untracked file
1. -f: force
2. -d: recurse  into directory 



## git config


config dir
```
/etc/gitconfig: system
 ~/.gitconfig: global
.git/config

```

```shell
git config --global  user.name atony2099
```

~/.gitconfig:
```shell
[user]  # git commit user 
  email = atony2099@gmail.com
  name = atony2099
  

[format]   #  default log format
  pretty = oneline

[alias]
 s = status
 lg1 = log --graph --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold cyan)%aD%C(reset) %C(bold green)(%ar)%C(reset)%C(bold yellow)%d%C(reset)%n''          %C(white)%s%C(reset) %C(dim white)- %an%C(reset)' --all
 

[url "https://gitlab.treesmob.com/"]  
    insteadOf = git@gitlab.treesmob.com:
```




## git status  and log 
```bash
git status 
git diff  [head]
```



1. git status : show  status change
2. git diff:  git diff one  two -- [path]

   1. git diff [commit] -- [path]: compare curret working to specify commit
   2. git diff commit1 commit2 [--][path]: compare the files in two commit
3. git diff --cached [commit2] [--][path]: compare staged tree vs specify commit
4. git show：

###  log

show the commit  log

`git log [[--] path]   [branch]`

1. parameters:
   1. -n: latest n commit;
   2.

2. format

   `git log --format[pretty]=` *oneline*, *short*, *medium*(default value), *full*, *fuller*, *reference*, *email*, *raw*,

   ```
   
   # medium
   commit <hash>
   Author: <author>
   Date:   <author date>
   <title line>
   <full commit message>
   ```

   ```shell
   git log --graph --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold cyan)%aD%C(reset) %C(bold green)(%ar)%C(reset)%C(bold yellow)%d%C(reset)%n''          %C(white)%s%C(reset) %C(dim white)- %an%C(reset)' --all
   
   git log --oneline    # EQUAL:  git log pertty=oneline --abbrev-commit
   
   ```

   > * efb96af - Fri, 28 May 2021 19:47:42 +0800 (2 days ago) (HEAD -> refactor_tang)
   > |           update router - atony2099

   1. --abbrev-commit: abbreviation;
   2. --graph: graphical representaton;
   3. --decorate[=short|full|auto|no]: howt to print the ref  names

## git commit

### 1. how commit  work

![](https://git-scm.com/book/en/v2/images/data-model-3.png)

every commit store in git datebase: .git/objects;

![s2wER2](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210526/s2wER2.png)



## git reference

a alias of commit;

Contain  branch and tag

### 1. Git branch(refers)
![data-model-4.png](https://git-scm.com/book/en/v2/images/data-model-4.png)
![](https://git-scm.com/book/en/v2/images/data-model-4.png)

```
.git/refs/
.git/refs//heads
.git/refs//heads/develop
.git/refs//heads/master
.git/refs//tags
```

HEAD: point currrent  branch;

```
cat .git/HEAD
```

### 2. tag

also is a alias of commit

1. tag basic operation?

   ```
   git branch
   ```

2. tag vs  branch?

1. both alias of a commit, point to a commit;

2. diff:

      branch will change as  a new commit is commited;  tag always point to a commit;

   > in practice, tag is as software version

### 1. what's  branch acutally is？

a branch is a pointer to specify commit; git merege just move the point the the specify commit;

1. fast-forward
![](https://wac-cdn.atlassian.com/dam/jcr:b87df050-2a3a-4f17-bb80-43c5217b4947/07%20(1).svg?cdnVersion=1607)

2. 3-way merge: generate a new commit

![](https://wac-cdn.atlassian.com/dam/jcr:91b1bdf5-fda3-4d20-b108-0bb9eea402b2/08.svg?cdnVersion=1607)

### 2. branch operation

```shell
git branch develop  master # create a branch from master

```

###

## 5. git  revert: if

git record the log of file change;
git get history information by  changin the point;

merge: put a forked history back again;4. git  revert

git record the log of file change;
git get history information by  changin the point;

merge: put a forked history back again;

## fetch and pull


```
git fetch <remote> <branch>
git pull <remote> <branch>
```


git fetch: get  the new commited of branchs 
git pull: git fetch + mreget

```shell
 git pull [origin  develop]

 git fetch origin develop
 git merge origin/develop
```

## checkout

switch branch or  restore working directory

1. switch branch

```
git checkout develop
```

2. restore working tree files：--
-- seperate file and trees

```
git checkout -- a.txt # 
```

## git ignore
/aaa:
ignore file or dir which named aaa   in root level 



```bash
/aaa # match any file or directory in root level
aaa/ # match any directoires with name aaa

test # match any file or any directories with the name test
```



## git clean

remove untracked file;
git clean -df;

-d: remove director
-f: force

## git branch manage

```shell
git branch newbranch  frombranch
```

git branch    


##  git remote  operation


git clone:
```bash
git init
git remote add origin repository-url 
git fetch # get all remote branch info 
git checkout  master
```

remote-url:
1. https: need account 
2. ssh: 



create a copy of another  responistory

### 1. what's  remote repository

As you clone from a another repository, the  repository become you  remote rep;

you also create remote-tracking branch which are references to the state of remote branches;

you can't change the remote-tracking branch;

```
remotes/orign/develop
remotes/orign/master
````

upstream-branch == the tracking branch;

1. create track with remote branch

```shell
# have create a new branch
git push -u origin master

# don't create a new branch
git branch develop origin/develop 

git checkout --track origin/develop
```

git remote add <remote shortname> <URL>
git push <remote> <branch>

git push --set-upstream <remote> <branch>

1. upstream branch
   remote branch track local branch





