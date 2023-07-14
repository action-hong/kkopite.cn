---
title: git撤销部分提交并推送
date: 2023-07-14T17:00:00.000+00:00
lang: zh
duration: 5min
tag:
  - git
---

在学习`git`时，我们最早会接触到`git reset`这个指令用来回退到过去的提交，但是当我们在多人协同时，用这个方法来撤销你自己的提交就显得不那么好用了。

例如一开始的提交时A, 你做了写功能并提交了B和C两个提交, 并推送到远程：

```
A -> B -> C
```

接着你的同事拉取了最新的代码，又做了写功能，提交了D:

```
A -> B -> C -> D
```

这时候你如果再本地回退到A就会变成：

```
A
```

将同事的D提交也弄没了，而且要与远程的提交历史也不一致了，使用`git push -f`强行推送上去的话，那就导致同事本地的提示历史和远程不一致了。

这时候我们可以使用`revert`命令:

```
git revert B...C

# or, 但这样会生成多个提交
git revert C
git revert B
```

该指令会将B到C的提交全部撤销并形成一个新的节点E:

```
// E 等价于 (BC)^-1
A -> B -> C -> D -> E
```

这样你就既可以将之前的提交撤销掉，又不影响提交历史了。

---

然后发现了另一个[方法](https://stackoverflow.com/questions/1463340/how-can-i-revert-multiple-git-commits/1463390#1463390), 如果你是要撤销从 B到D的提交，且远程D就是最新的提交点了，你可以这么干：

```
# 回到A提交
git reset --hard A
# 使用--soft选项回到 D节点，暂存区保留从D到A的修改 （即同等于从D撤销到A）
git reset --sort D
# 提交生成新节点，完成了撤销同时又保证提交历史正常
git commit -m"some commit message"
```





