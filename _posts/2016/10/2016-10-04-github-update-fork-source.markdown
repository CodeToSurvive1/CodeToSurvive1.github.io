---
layout: "post"
category: "macaca"
title: "github从源代码库更新到自己fork的代码库"
tags: [macaca,nodejs]
date: "2016-10-04 22:42"
---

# github从源代码库更新到自己fork的代码库
<br>
### 1.以自己从macacajs源代码库中fork的https://github.com/CodeToSurvive1/app-inspector.git为例子
切换到本地app-inspector目录中

```xml
mac:app-inspector mac$ git remote -v
origin	https://github.com/CodeToSurvive1/app-inspector.git (fetch)
origin	https://github.com/CodeToSurvive1/app-inspector.git (push)

```

### 2.添加上游源代码的仓库地址

```xml
mac:app-inspector mac$ git remote add upstream https://github.com/macacajs/app-inspector.git
mac:app-inspector mac$ git remote -v
origin	https://github.com/CodeToSurvive1/app-inspector.git (fetch)
origin	https://github.com/CodeToSurvive1/app-inspector.git (push)
upstream	https://github.com/macacajs/app-inspector.git (fetch)
upstream	https://github.com/macacajs/app-inspector.git (push)
```

如果不小心添加错了，可以删除

```xml
mac:app-inspector mac$ git remote remove upstream
```

### 3.从远程下载分支

```xml
mac:app-inspector mac$ git fetch upstream
From https://github.com/macacajs/app-inspector
 * [new branch]      fix-bounds-parse -> upstream/fix-bounds-parse
 * [new branch]      gh-pages   -> upstream/gh-pages
 * [new branch]      master     -> upstream/master
 * [new branch]      support-real-device -> upstream/support-real-device
```

### 4.检出本地分支

```xml
mac:app-inspector mac$ git checkout master
Already on \'master\'
Your branch is up-to-date with \'origin/master\'.
```

### 5.合并远程分支的变化到本地master仓库，这一步会合并远程代码库中的更新到本地仓库中，不会丢失本地的修改

```xml
mac:app-inspector mac$ git merge upstream/master
Updating e0695e8..6f8c6a7
Fast-forward
 .gitignore                   |  1 +
 .npmignore                   |  1 +
 assets/components/index.jsx  |  6 +++---
 assets/components/screen.jsx | 11 ++---------
 assets/components/tree.less  |  2 +-
 assets/index.less            |  1 +
 assets/libs/bounds.js        | 51 ++++++++++-----------------------------------------
 lib/android.js               | 19 +++++++++++--------
 lib/ios.js                   | 63 +++++++++++++++++++++++++++++++++++++++++----------------------
 package.json                 |  3 ++-
 public/dist/index.js         |  8 --------
 11 files changed, 73 insertions(+), 93 deletions(-)
 delete mode 100644 public/dist/index.js
mac:app-inspector mac$
```

### 6.提交到github仓库

```xml
mac:app-inspector mac$ git push origin master
Everything up-to-date
mac:app-inspector mac$
```
