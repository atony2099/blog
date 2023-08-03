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

## branch

###  how commit  work

![](https://git-scm.com/book/en/v2/images/data-model-3.png)

every commit store in git datebase: .git/objects;

![s2wER2](https://cdn.jsdelivr.net/gh/atony2099/imgs@master/20210526/s2wER2.png)



### git reference

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



## git workflow


### no-ff

```bash
git merge --no-ff
```

1. create a commit : 方便查询和回退


![XdgK0w7G6IqI](https://cdn.jsdelivr.net/gh/toms2077/imgs@master/20230729/XdgK0w7G6IqI.png)




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





## git commit

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

