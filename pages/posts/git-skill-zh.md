---
title: git的一些笔记
date: 2023-07-06T16:00:00.000+00:00
lang: zh
duration: 5min
tag:
  - git
---

## git 生成和使用 patch

### 创建 patch

```bash
// 表示从 commit-id这个提交算上的 n个提交的 patch
git format-patch commit-id -n

// 表示 创建 commit-id 这个提交的patch
git format-patch commit-id -1
```

### 使用 patch

```bash
// 检查 patch 是否能正常打入
git apply --check ./path/to/patch

// 打入 patch
git apply ./path/to/patch
// 或者
git am ./path/to/patch
```

## 推送浅克隆的项目到新仓库

最近要将一些以前的旧项目全放到 gitlab 上，有些项目推送时发现如下问题：

```bash
git push -u origin --all
[...]
 ! [remote rejected] master -> master (shallow update not allowed)
```

这是因为之前项目是从别人的模板浅克隆下来修改的，别人的提交我们不需要，而且这样很快：

```bash
git clone --depth=1 xxxxx
```

但现在就麻烦了，没法推上去，搜索了一下发现有个[方法](https://stackoverflow.com/questions/50992188/how-to-push-a-shallow-clone-to-a-new-repo)很管用，特做下记录

```bash
# 切换到我们本地第一条提交, 使用git rev-list main 查询第一个提交点
git checkout $START_COMMIT

# 以第一条提交作为一个新的初始提交点，因此使用了 --orphan
git checkout --orphan temp_branch

# 提交，现在这个是新的初始提交点了
git commit -m "Initial commit"

# 将main的所有提交点以新的提交为初始点，这样就可以提交了
git rebase --onto temp_branch $START_COMMIT main

# 推送
git push -u origin main
```