---
title: '在 Git 中使用子模块'
date: '2021-10-05'
tags: ['技术']
---
## 背景

在 [关于 Fruition 问题的反思](../reflections-of-fruition) 中我提到因为 Loconotion 方面的原因，我自己 fork 并修改了一遍 Loconotion 项目的部分文件，如今原项目已经修复了这个 bug，虽然一时半会儿并没有迁移回去的必要，但是思来想去还是用官方的仓库比较好，一来自己修改的时候有个人化的定制，可能会泄露一些隐私（其实并不算），二来以后也比较好与上游同步。事实上，除去链接的问题，我也就改了 Google Fonts 的国内镜像，这个问题不大不小，其实也没什么好纠结的。

废话说太多了，本篇文章的目的是记录一下子模块的使用，以便之后不必再四处寻找教程手忙脚乱。

## 删除子模块
💡 以下命令行操作是在主仓库目录下进行的。
1. 删除子模块缓存

```bash
git add .gitmodules
git rm --cached submodule_name
```

2. 删除子模块目录

```bash
rm -rf ./submodule_name
```

3. 修改 `.gitmodules` 文件
    
    移除相关模块的信息，若只有一个，则可以直接删除此文件。
    
4. 在 `.git/modules` 目录下删除相关模块目录
5. 在 `.git/config` 删除相应的模块信息
6. 提交到仓库，做其他想干的事情

## 添加子模块

1. 添加模块

```bash
git submodule add <url> <path>
```

2. 报错： `git submodule: already exists in the index` 
    
    先运行下列命令，再重复 1. 步骤
    

```bash
git rm -r --cached submodule_name
```

## 使用子模块 
##### *实测 Github Desktop 客户端可以跳过此步骤。

默认在克隆主仓库时不会克隆子仓库的文件，需要手动克隆

```bash
git submodule init
git submodule update
```

或者

```bash
git submodule update --init --recursive
```

## 更新子模块到最新提交

在模块的原仓库更新后，使用该模块的主仓库需要手动更新才能包含最新的提交。

1. 进入子模块目录
2. 抓取并查看最新的三个提交

```bash
git fetch
git log --oneline origin/master -3
```

3. 切换到最新提交分支

```bash
git checkout -q XXXXX
```

4. 将更改提交并推送到仓库即可

## Ref:

1. [guotianqing —— 《git中submodule子模块的添加、使用和删除》](https://blog.csdn.net/guotianqing/article/details/82391665)
2. [朽木白露 —— 《git 在 A 项目中引用 B 项目》](https://blog.csdn.net/qq_45467083/article/details/108731068)
3. [devconnected —— 《How To Add and Update Git Submodules》](https://devconnected.com/how-to-add-and-update-git-submodules/#Update_a_Git_Submodule)