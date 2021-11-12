###### 1.初始化git仓库

```bash
git init
git config --global user.name  "username"  
git config --global user.email  "email"
```

###### 2.克隆仓库到本地

```bash
git clone https://github.com/cxhello/springboot-admin.git
```

###### 3. 把从来没有被标记过的文件放入暂存区 

```bash
git add .   所有文件
git add -A  既能将已加入暂存区文件的改动放入暂存区,也能将未加入暂存区文件的改动放入暂存区
git add 文件名
git add 文件夹名
git add -u  它只能将已加入暂存区文件的改动放入暂存区
git stage
```

###### 4. 显示工作目录和暂存区的状态 

```bash
git status
```

###### 5.提交

```bash
git commit -m "提交信息"
git commit --amend  允许修改最近一次的commit
```

###### 6.创建分支

```bash
git branch dev
```

###### 7.查看本地仓库有多少分支

```bash
git branch
```

###### 8.删除分支

```bash
git branch -d dev
git branch -D dev
```

###### 9.切换分支

```bash
git checkout dev
```

###### 10.创建分支并切换

```bash
git checkout -b dev
```

###### 11.合并分支

```bash
git merge dev
```

###### 12. 遇到冲突以后不知道如何解决，因为你要去询问你的合作伙伴为什么这样改。这时你肯定想回到合并以前的状态。 

```bash
git merge --abort
```

###### 13.你在一个分支上开展了一半的工作，突然有一件急事要你去处理。这时候你得切换到一个新的分支，可是手头上的工作你又不想立即提交。这种场景就需要用到git的储藏功能。

```bash
git stash
git stash save "save message"
```

###### 14.查看储藏列表

```bash
git stash list
```

###### 15. 等我们完成其他工作，肯定要回到这里，继续进行中断的任务。 

```bash
git stash apply
```

###### 16.清理储藏列表

```bash
git stash drop stash@{1}
```

###### 17.同时恢复储藏和清理储藏。 

```bash
git stash pop
```

###### 18.拉取代码

```bash
git fetch
git pull
强制更新
git pull origin master --allow-unrelated-histories
```

###### 19.推送到远端

```bash
git remote add origin <url>
git push origin master
```

###### 20.显示提交历史

```bash
git log
git log --oneline
```

###### 21.重置

```bash
git reset 版本号
git reset --mixed 版本号  默认值,暂存区的内容会重置为重置后的commit内容，工作区的改动不会清空，相当于撤销暂存区的改动。
git reset --hard 版本号   暂存区和工作区的内容都会重置为重置后的commit内容。也就是说暂存区和工作区的改动都会清空，相当于撤销暂存区和工作区的改动。
git reset --soft HEAD~1  重置本次提交
```

###### 22.版本打标签

```bash
git tag <tag name>
git tag v1.0
git tag -a v1.0
git tag -a v1.0 36ff0f5
git tag –a v1.0 –m '备注内容'
```

###### 23.删除版本

```bash
git tag -d v1.0
git push origin -d v1.0
```

###### 24.本地及远程回退

```bash
git log --oneline  查看提交历史
git reset --hard 版本号  本地版本回退
git push origin HEAD --force  远程版本回退
```

