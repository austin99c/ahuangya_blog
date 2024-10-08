---
id: git-ignore-idea-file
slug: git-ignore-idea-file
title: git 忽略.idea文件
date: 2024-10-05
authors: loner
tags: [git]
keywords: [git]
---

<!-- truncate -->
:::tip
在进行GitHub/GitLab上传本地代码时，需要注意一下，尤其是我们第一次上传时，.idea文件会在git push代码时，默认自动一并上传到GitHub/GitLab上。

如果你觉得Git仓库不应该包含和项目本身无关的文件，那么也可以不将它提交到Git仓库中。
:::

## 一、.idea文件的作用
.idea文件与当前项目能否正常执行无关，它只是负责对代码的历史变化进行一个记录，便于回溯查找和复原。

## 二、如何忽略.idea文件
下面就来说说如何在提交本地代码到GitHub/GitLab，忽略.idea文件。

### 2.1 创建.gitignore文件
如果没有.gitignore文件，则创建一个，然后写上 /.idea/ 这一行。
```shell
$ vim .gitignore
/.idea/
```

### 2.2 提交忽略/.idea/的变更
```shell
Git rm -r --cached .idea       #### --cached不会把本地的.idea删除
 
Git add .
 
Git commit -m 'ignore idea'
 
Git push
```