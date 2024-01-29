---
title: git
date: 2021-03-14T15:35:06+0800
categories:
  - git
lastmod: 2023-03-20T09:52:44+0800
draft: true
tags:
  - git
---

[The Most Basic Git Command List](https://www.tutorialdocs.com/article/git-basic-command-list.html)

[Git Internals - Git References](https://git-scm.com/book/en/v2/Git-Internals-Git-References)

[git-log](https://git-scm.com/docs/git-log)

[A successful Git branching model » nvie.com - https://nvie.com/posts/a-successful-git-branching-model/](https://nvie.com/posts/a-successful-git-branching-model/)

## git overview

![OwXxW0Qt3mNC](https://cdn.jsdelivr.net/gh/toms2077/imgs@master/20230729/OwXxW0Qt3mNC.png)


![KJFkJw](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210331/KJFkJw.jpg)
![usyT2X](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210331/usyT2X.jpg)
![e6VPjC](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210331/e6VPjC.jpg)


## working/stage/repo 


%%working tree: 本地的草稿纸，可能有很多草稿纸
staging area: 已经完成的章节的复印件
local: 已经完成的章节并完成编辑部审核的复印件
remote:  完成审核后存储在银行保险箱复印件%%


working: 工作区, one  history change timeline, a->b->c 
staging: 暂存区,   one history change timeline, a->b->c
repo:   repo, multiple  history   change timeline 
remote:  another repo 


why need stage area: 允许部分提交草稿
working tree is clean:    working stage and repo 完全同步




## command list

git clean: remove untracked file

```
git clean:
     -n: remove  
	 -f: force remove; 
	-d:  remove untrack dir
git clean -df
```


### reset change

git reset \[--mixed head\]:  sync stage/working to last commit
git checkout -- filename:   discard  change  in working  


discard all change 
```bash
git reset --hard 

```

discard some file change:
```
git reset --mixed a.txt
git checkout -- a.txt
```


## git config


config dir:
```shell
/etc/gitconfig: --system
 ~/.gitconfig: --global
.git/config: --local
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
git diff  [head|commit|branch] -- file # compare working with some commit 
git diff  [commit|branch|] [second] -- file 


```

git diff:



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

## branch/commit/ tag

###  how commit  work

![](https://git-scm.com/book/en/v2/images/data-model-3.png)

every commit store in git datebase: .git/objects;

![s2wER2](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210526/s2wER2.png)




###  Git branch(refers)
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

###  tag/commit/branch

commit: 提交的节点；

tag: 某个commit的alias

branch:  包含一组提交，branch pointer  指向这组提交的头部；
```
A -  B ---D------F   branch main
      \         /
       C---- E  branch(A)

branch main:  a,b,d,f
branch A: a, b c e;
```

commit 与他的 first  parent commits 构成 branch
1. branch start: 创建branch 的起始commit
2. branch header: 当前最新的commit, 自动更新


### tag

what: commit的别称, 通常指向的是某个具有意义的commit，如准备上线的commit
pros: 更方便进行管理， 可以基于容易识别的tag进行查询，回滚等操作


```bash
git tag v1.0.0
git tag # list all tag 

#tag需要独立push
git push origin v1.0.0
git push origin --tags 
```




### fast-forward 

![XdgK0w7G6IqI](https://cdn.jsdelivr.net/gh/toms2077/imgs@master/20230729/XdgK0w7G6IqI.png)


fast-forward merge:
在原分支没有 新的commit时候，
1. 原分支将branch head 指向被合并分支的最新的commit, 
2. 被合并的分支的commit 成为原分支历史 commit的一部分

plain merge  :
在原分支没有 新的commit时候，
1. 原分支创建一个新的commit, 该commit 包含被合并分支的所有改动
2. 被合并的分支的commit 不成为原分支历史commit的一部分


```
A - B - C        (master)
         \
          D - E  (feature)


A - B - C - D - E

A - B - C -------F 
		 \      /
		  D - E 

```





## git ignore


file:
```bash
/aaa #  in root, file or dir
aaa/ # in anywhere, dir 
aaa # in anywhere, file or dir



.vscode/*
!.vscode/settings.json
!.vscode/tasks.json



```

global file:  不同语言都要ignore, 
1. os
2. editor: vscode, pycharm
```
git config --get core.excludesfile
```




## git clean

remove untracked file;
git clean -df;

-d: remove director
-f: force


##  git remote  

git clone:
```bash
git init
git remote add origin repository-url 
git fetch # get all remote branch info 
git checkout  master
```

remote-url:
1. https: need account and password   
2. ssh:  config public key  



git fetch and pull 
```bash
git fetch <remote> <branch>
git pull <remote> <branch>

git pull origin dev 

git fetch origin dev
git merge  origin/dev
```







## git revert  &  rollback 
![XdgK0w7G6IqI](https://cdn.jsdelivr.net/gh/toms2077/imgs@master/20230729/XdgK0w7G6IqI.png)

git reset: 回退 <到>某个commit   working/stage/commit 
git restore: 回退<到>到某个commit，  working/stage 
git revert:   撤销某个、某些节点， 并创建 n个commit   



```bash 
A -- B -- C -- D -- E (HEAD)

# reset to last third commit 
git revert head~1..head 
git  reset  head~2


# reset the working to  last third commit
git restore --source=head~2



# reset the working and staged to head
git  restore * 
git restore --staged *

```

head~n or head^^^^...: move back to nth commit in current branch 
head^n: move to nth parent of current merge, use for merge commit

```shell
    A - B - C feature
   /          \
D - E - F - G - M main (HEAD)


head: m
head^: first paret of current merge:  G
head^2: 2th parent of current merge:   c 

head~2: F 
```

## commit msg

parameter:
1. -a:   stage all tracked file


how to  write  commit messae:
1. short summary
2. detail 
3. include reference to issue if necessary 

```bash
#body
[add|fix|refactore] something
# the blank line is necessary  for disguish the title and body 
#detail
- detail1
- detail2
- detail3

reference
```

b


```md
Add search functionality to user dashboard

- Implement full-text search for user profiles
- Include filters for age, location, and interests
- Optimize search query for performance

Related to issue #456


Fix crash in payment processing

- Resolve null pointer exception in payment gateway
- Add validation checks for payment details
- Update tests to include edge cases

Closes #789


```






##  git workflow 
workflow: 一系列建议使用好git 

long-live:
main branch: 当前发布的最新版本
develop:  
1. 包含最新的，已完成的功能;(最新代码在feature)
2. 可能还有bug;


short live:
feataure/abc
release-1.2: 发布前小的修改 
hotfix: 为什么需要： 
develop代码还不稳定，直接checkout -b develop 可能会发布不想发布的代码


```

git checkout -b feature/a develop;

// in branhch feature:  finish develp and test
feataure/a

commit;
commmit;


// merge feature, if find small bug, fix in develop, if haev big bug or change, create a new feautere banch to develo
git checkout develop;
git merget feature/a;
git branch -d feature/a 
commit -am 'small bug fix '



// prepeare to  relase
git checkout -b   relase-1.2 develop 
// small fix
commit -m 'small bug fix'



// merge to maseter

main: git merge relase-1.2
develop: git merge releae-1.2


// hotfix
git checkout -b hotfix-1.2.1 main
commit -m 'fix bug'

main: git merge  hhotfix-1.2.1
develop: git merge   hhotfix-1.2.1


```


when finish feature:
1.  finish development and testing 
find bug after mergre feature:
1.  samll bug,  in develop 
2. big bug: new branch


## git stash
what：
temp to save the change in a place, 

use case：
when current work not finish, but need  work with have commit code; 

how:
```bash
git stash push -u -m  'user change' 
// -u:  include the untracked file 
// -a: -u + include igntore file

git stash drop stash@{0}: remove the stash 
git stash apply stash@{0}： use the stash 


git stash pop:  git stash apply lates and git stash dorp latest


```


## git  basic command
git push:
git push   [  origin  ]  local_branch


set upstream to:  配置分支的上游分支；上游分支也是一个分支;
git push -u  orgin main: 


git  pull:

git merge
