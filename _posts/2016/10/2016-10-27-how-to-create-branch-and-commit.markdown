---
layout: "post"
title: "如何创建分支并提交代码"
date: "2016-10-27 08:05"
tags: [macaca,git]
category: "other"
---


#### 如何创建分支并提交代码  

1.首先查看分支信息  

```git

git branch
* create
  dev
  master

```

2.创建分支test  

```git

git branch test

查看分支是否创建成功  
git branch

* create
  dev
  master
  test
```

3.切换分支到test   

```git

git checkout test
Switched to branch 'test'

查看是否切换成功
git branch
  create
  dev
  master
* test

```

4.修改文件信息，比如这里添加一个文件


```git

touch test
ls
macaca-test	package.json	test

```

5.由于添加了新文件，所以需要提交新文件上去  

```git
添加当前目录
git add .

提交到本地仓库
git commit -am '添加test文件'
[test 4d485cd] 添加test文件
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 test

推送到远程仓库
git push origin test
Counting objects: 3, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 286 bytes | 0 bytes/s, done.
Total 3 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local objects.
To https://github.com/CodeToSurvive1/bank_sample.git
 * [new branch]      test -> test

```

6.直接通过web页面管理器查看是否已经提交到git上

![成功截图](../assets/2016-10-27_08-43-27.png)  

![成功截图2](../assets/2016-10-27_08-53-49.png)  


7.如果想删除本地分支或者远程仓库分支

```git

git branch -D test
Deleted branch test (was e7c582b).

```


删除远程仓库分支  

```git
$ git push origin --delete test
To https://github.com/CodeToSurvive1/bank_sample.git
 - [deleted]         test

```
