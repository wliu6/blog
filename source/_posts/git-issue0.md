---
title: Git —— 数据恢复
date: 2017-07-23 14:30:46
tags: git
categories: development
copyright: true
---

> 使用git硬回滚操作错误，导致马上要提交测试的代码丢失。研究了一下git数据恢复，并总结记录。

<!--more-->

---

<br/>
# 起因

早上回滚代码时，使用了错误的SHA-1值，导致丢失了一个明天要提测重要功能代码片段。
```
$ git reset --hard 96edccd4ed9ced23296e8ccfdc78f89078177107
```
解决问题的方法有两种，一种是直接重新编写该功能的代码，另一种是研究git的数据恢复。
(Ps：作为一名有上进心的程序猴，我还是抱着能get新知识点的心态去学习了一下git的数据恢复。)



<br/>
# 如何恢复commit数据

git数据恢复其实很简单，总共分两步。第一步，查找丢失的信息
```
$ git reflog
```
找到丢失的信息了，`feature-1127`这个是被删除的有用代码。

	3c0437194 HEAD@{1}: reset: moving to 3c043719499bfde00340fb9358c115f815bfe2a7
	6cd4ee3dc HEAD@{2}: commit: refuse code
	96edccd4e HEAD@{3}: checkout: moving from Debug to merge_tmp
	96edccd4e HEAD@{4}: pull: Fast-forward
	6bd181333 HEAD@{5}: checkout: moving from network to Debug
	ee3dc6cd4 HEAD@{6}: commit: feature-1127
	3c0437194 HEAD@{6}: commit: feature-1126
	...

第二步，根据正确的SHA-1值回滚到应该回滚的位置。
```
$ git reset --hard ee3dc6cd4
```

验证是否成功找回丢失的提交index，
```
$ git log -1
```
最新的提交是`feature-1127`，证明成功找回丢失的提交。

	commit c1c3f278d959cf108b05ba8268321d01d73da8cf (HEAD -> Develop)
	Author: 6 <wangduorui@kaistart.com>
	Date:   Fri Jul 20 18:43:37 2016 +0800

	    feature-1127




<br/>
# 如何恢复branch数据

和恢复commit数据一样，只需要两部就可以恢复本地误删的`branch`。第一步，查找丢失的分支
```
$ git reflog
```
很容易看出`HEAD@{2}`是被删除的分支的HEAD指向。

	c1c3f278d (HEAD -> Develop, ksb/Develop) HEAD@{0}: checkout: moving from test_br to Develop
	0f9ef0947 HEAD@{1}: commit: test
	c1c3f278d (HEAD -> Develop, ksb/Develop) HEAD@{2}: checkout: moving from Develop to test_br
	87bdd6dbf HEAD@{3}: commit: fixed unread
	30b0d8220 HEAD@{57}: commit: web hook
	...

第二步，基于`HEAD@{2}`创建被删除的分支。
```
$ git branch lost-branch 0f9ef0947
```



<br/>
# pick知识点

**reflog**是Reference Logs的缩写，意为引用日志丨调用日志，用于记录本地仓库的分支、更新操作（即：每一次HEAD改变）。
<!-- 前文的数据恢复只是对于丢失的`commit`的恢复，按照`reflog`记录的信息，还可以恢复本地误删的分支。 -->




<br/>
# Tips

## 关于reset
执行`reset`操作并不会改变SHA-1值，所以在使用`reflog`工具查看日志时，会发现两个相同的SHA-1值的HEAD，可以帮助我们找到误删的`commit`。

## 关于branch
由于`git`不允许删除当前指向的分支，所以在删除分支之前的操作一定是切换分支`checkout:`。而且`reflog`工具在日志中会直接记录`moving from A_branch to B_branch`，所以直接按被删除的分支名进行查找即可。



<br/>
# 总结
对于硬回退可能导致误删有效`commit`，并且需要通过`reflog`工具才能找回；有分析成本，相对麻烦；建议在撤销无用的`commit`时，使用revert。
对于删除分支尽量先使用`git branch -d <branch-name>`，不要直接使用`git branch -D <branch-name>`。因为`git branch -D <branch-name>`是直接强制删除分支，而`git branch -d <branch-name>`会check一下当前处于分支是否有落后于删除目标分支的提交，如果有会不执行删除，并输入警告，这就让脑子有个思考过程，删除的分支改的东西是否真的没用。
