---
title: git常用指令
date: 2019-08-27
desc:
keywords: git command 
categories: [tool]
---

# 工作区与暂存区
    
- 版本回退：
    
```
git log (git log --pretty=oneline) 查看提交日志
git log --graph --pretty=oneline --abbrev-commit 简化查看日志
git reset --hard commitid(HEAD^ HEAD表示当前版本 ^表示上个版本 以此类推,也可以使用~num,num表示几个^) (windows命令行下需要git reset --hard "HEAD^" ) 
git reflog 查看命令历史
```
    
- 管理修改：
    
```
git add xx 把文件添加到版本库
git status 查看当前版本库状态
git commit -m "msg" 提交修改 -m表示提交注释 ps:若使用命令行提交，每次commit都需add
git diff 
git diff HEAD -- xx.file 查看工作区和版本库里面最新版本的区别 (HEAD^ 与版本回退同理)
```

- 撤销修改：
    
```
git checkout -- xx 把文件在工作区的修改全部撤销。第一种情况修改后未被放入暂存区，撤销修改与版本库一致，第二种已经添加到了暂存区，又作了修改，撤销修改回到add时的状态。让这个文件回到最近一次git commit或git add时的状态

git reset HEAD <file> 把暂存区的修改回退到工作区,适用于add未commit
```

- 删除文件：
    
```
git rm 用于删除一个文件
```

- 取消文件跟踪：
    
```
git rm --cached xx.txt 删除readme1.txt的跟踪，并保留在本地。之后git commit 

git rm --f xx.txt 删除readme1.txt的跟踪，并且删除本地文件。之后git commit
```

# 远程仓库
    
- 添加远程库：
    
```
git remote add origin xx.git 添加远程仓库地址
git push -u origin master 由于远程库是空的，我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令
git push origin master推送最新修改
```

- 从远程库克隆：
    
```
git clone xx.git
```

- 多人协作：
    
```
git remote -v 查看远程库信息
git fetch origin <branch-name> 从远程拉去分支代码
git merge 合并代码
git pull = git fetch + git merge
git push origin <branch-name> 推送修改
git checkout -b <branch-name> origin/<branch-name> 本地创建和远程分支对应的分支,名称最好一致
git branch --set-upstream <branch-name> origin/<branch-name> 建立本地分支和远程分支的关联
```

- git放弃本地修改，强制更新：
    
```
git fetch --all 下载远程仓库最新内容，不做合并
git reset --hard origin/master 把HEAD指向master最新版本
git pull
```

# 分支管理
    
- 创建与合并分支：
    
```
git branch dev 创建dev分支
git checkout dev 切换至dev分支
git checkout -b dev 创建并切换至dev分支
git branch 查看当前分支
git merge dev 合并dev分支代码至当前分支
git branch -d dev 删除dev分支
```

- 解决冲突：
    
```
git log --graph 命令可以看到分支合并图
```

- Bug分支：
    
```
git stash 暂存当前工作区代码
git stash list 查看暂存代码记录
git stash apply 恢复代码至工作区，但不删除暂存内容
git stash drop 删除暂存内容
git stash pop = git stash apply + git stash drop 恢复代码至工作区并删除暂存内容
git cherry-pick <commitid> 复制某一次提交代码至当前分支
```

# 标签管理
    
- 创建标签：
    
```
git tag <tagname> 用于新建一个标签，默认为HEAD，也可以指定一个commit id
git tag -a <tagname> -m "msg" 可以指定标签信息
git tag 查看所有标签
git show <tagname> 查看说明
```

- 操作标签：
    
```
git tag -d <tagname> 删除一个本地标签
git push origin :refs/tags/<tagname> 删除一个远程标签
git push origin <tagname> 推送标签至远程
git push origin --tags 一次性推送全部尚未推送到远程的本地标签
```

