# GIT的学习

### 一 常用指令

```
git init
git add README.md
git commit -m "first commit"
git remote add origin https://github.com/liyuan1001/nothing.git
git push -u origin master            
```

### 二 GIT学习心得

​	对于GIT来说我们平时如果简单使用我觉得上面的几个指令就足够，但是遇到情况复杂的时候，就会有些懵。例如，协同开发，分支合并等事情就会觉得很麻烦，可能学习的不到位吧，对git的原理了解不够深入因此会产生这问题。
​	学习一个东西就要精益求精，不能存在模糊的点，这样才能与众不同，让学习变的效率不能过目就忘。
​	因此总结一篇详细的操作说明，原理分析供自己或者看到人使用分享。

### 三 GIT的学习

​	学习主要针对本地仓库，与远程仓库的学习，以及合并本地仓库，或者上传到远程仓库等，以及分支管理等内容

#### 3.1 本地仓库的创建以及操作

​	对于本地空仓库，选择一个目录git init 即可（会出现一个.git文件）
​	然后创建一些文件之后”git add .“即可，相当于把所有的文件添加到暂存区
​	最后git commit -m "注释"就把暂存区的内容提交到本地仓库
​	对于本地仓库来说一个基本的流程就已经完成了
​	但是还会涉及的很多其他的内容，比如说我后悔提交了，我想去一个新的分支继续操作等等内容
​	当我们想看我们提交文件的状态时，git status即可查看
​	查看某个文件的变化可以通过git diff（很对用法以后再来比较）来查看
​	此时此刻，我后悔了，觉得还是上一个版本好于是可以通过git log查看历时的提交记录“--pretty=oneline”简化
​	然后发现上个版本好我们就可以使用git reset --hard HEAD^ 回退到上一个版本 HEAD~100（回退到上100个）
​	当我们回到上个节点之后在git log会发现之前的那个版本不在了，老板说还是之前的好要回去。
​	此时怎么办呢？如果没有关闭窗口，可以通过git reset --hard commit id来回去。
​	但是窗口关闭了，commit id找不到了咋整呢？通过git reflot即可查看到每一次的提交，上面会显示commit id。
​	夜深人静，头脑不清醒，我们修改了一个文件添加了一句可能被开除的话，想把它撤销回来git checkout -- filename可以帮助我们
​	很不幸我们提交到了暂存区，就用git reset HEAD file来删除暂存区的修改然后在撤销本地修改。
​	更不幸的是我们提交到了版本库，那只能git reset --hard回到上一个版本了。
​	这个时候我们想删除一个文件，然后发现版本库和本地工作区的数据不一致了：我们可以做两个操作
​	第一个操作就是git rm file 然后 git commit 就可以让版本库和本地一样
​	或者git checkout -- filename可以很轻松地把误删的文件恢复到最新版本
​	以上就是本地单分支的所有操作了，git的强大之一在于远程仓库，分支等

#### 3.2 分支的学习

​	通过一个实战学习一下分支管理这个功能吧：
​	创建一个dev分支，然后切换到dev分支:

```
git checkout -b dev = git branch dev  + git checkout dev
```

​	然后可以使用`git branch`查看当前的分支信息
​	我们修改一个文件之后并提交到本地仓库，然后再切回master分支`git checkout master`
​	然后我们去合并dev和master分支
​	使用git merge dev 即可合并分支内容，使用的是Fast-forward也就是直接把`master`指向`dev`的当前提交
​	然后就可以删除分支dev了`git branch -d dev`
​	switch也可以进行切换创建分支
​	`git switch -c dev`

​	**小结：**

```
查看分支：git branch
创建分支：git branch <name>
切换分支：git checkout <name>或者git switch <name>
创建+切换分支：git checkout -b <name>或者git switch -c <name>
合并某分支到当前分支：git merge <name>
删除分支：git branch -d <name>
```

上面是非常简单的情况，并不需要解决冲突的问题
但是我觉得很复杂的就是分支合并的时候存在了冲突

再通过一个案例进行学习：
准备新的`feature1`分支，继续我们的新分支开发：

```
$ git switch -c feature1
```

修改`readme.txt`最后一行
在`feature1`分支上提交：
切换到`master`分支：git switch master
在`master`分支上把`readme.txt`文件的最后一行修改
然后git merge feature1 就会发生冲突
手动修之后再次提交即可
git log --graph --pretty=oneline --abbrev-commit 查看合并分支情况

合并分支的时候可以使用`git merge --no-ff -m "merge with no-ff" dev`这样就不会丢失dev分支的更新信息了

##### 3.2.1 BUG分支

举一个案例学习吧：（其实都是复制粘贴来学习）
当我们在dev分支开发的时候，突然接收到了一个指令要修复master的一个bug,但是dev的工作开发到了一半。进退两难的境地，
所以这个时候要怎么办呢？

使用git stash保存现场
然后去往master分支建立bug分支，修改完之后进行合并，并删除分支
然后切回dev分支
使用git stash list来查看保存的现场
然后两种方法：
一是用`git stash apply`恢复，但是恢复后，stash内容并不删除，你需要用`git stash drop`来删除；
另一种方式是用`git stash pop`，恢复的同时把stash内容也删了

然后可以通过cherry-pick <<commit id>>快速的把master分支bug修改的代码合并到dev上



##### 3.2.2 feature分支

开发一个新feature，最好新建一个分支；

如果要丢弃一个没有被合并过的分支，可以通过`git branch -D <name>`强行删除。-d删除

##### 3.2.3 多人协作开发项目

我们可以通过git remote 查看远程分支， git remote -v 查看详细的远程目录
如果我们想去远程的dev分支进行开发`git checkout -b dev origin/dev`

```
小结

- 查看远程库信息，使用`git remote -v`；
- 本地新建的分支如果不推送到远程，对其他人就是不可见的；
- 从本地推送分支，使用`git push origin branch-name`，如果推送失败，先用`git pull`抓取远程的新提交；
- 在本地创建和远程分支对应的分支，使用`git checkout -b branch-name origin/branch-name`，本地和远程分支的名称最好一致；
- 建立本地分支和远程分支的关联，使用`git branch --set-upstream branch-name origin/branch-name`；
- 从远程抓取分支，使用`git pull`，如果有冲突，要先处理冲突。
```

##### 3.2.4 Rebase

git rebase 感觉自己一句两句也说不明白，看了网上的一堆讲解，简单得理解一下就是git rebase 会把合并之后的记录变成一条直线。
git merage则还是合并之后在原有的分支延申。

“另外，我们在使用git pull命令的时候，可以使用--rebase参数，即git pull --rebase,这里表示把你的本地当前分支里的每个提交(commit)取消掉，并且把它们临时 保存为补丁(patch)(这些补丁放到".git/rebase"目录中),然后把本地当前分支更新 为最新的"origin"分支，最后把保存的这些补丁应用到本地当前分支上。”

#### 3.3 标签管理

```
小结
命令git tag <tagname>用于新建一个标签，默认为HEAD，也可以指定一个commit id；
命令git tag -a <tagname> -m "blablabla..."可以指定标签信息；
命令git tag可以查看所有标签。
命令git push origin <tagname>可以推送一个本地标签；
命令git push origin --tags可以推送全部未推送过的本地标签；
命令git tag -d <tagname>可以删除一个本地标签；
命令git push origin :refs/tags/<tagname>可以删除一个远程标签。
```



https://gitee.com/liaoxuefeng/learn-java/raw/master/teach/git-cheatsheet.pdf 手册

https://git-scm.com/ 官网









