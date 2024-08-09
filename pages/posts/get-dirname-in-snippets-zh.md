---
title: vocde 的 snippets 中获取目录名称
date: 2024-06-15T22:00:00.000+00:00
lang: zh
duration: 5min
---

最近接手一个 `react` 项目，它的组件目录是这样子的：

```
components/
└── virtual-list/
    └── index.tsx
```

```tsx
// components/virtual-list/index.tsx
export function VirtualList() {
  // ...
}
```

可以看到组件名称就是起目录的名称的 PascalCase 形式：virtual-list -> VirtualList

在写代码片段时，可以通过如下代码来插入：

```
${TM_DIRECTORY/.*\\\\(.+)$/${1:/pascalcase}/}
```
解释：

- TM_DIRECTORY：即当前目录的路径
- `.*\\\\(.+)$`：正则表达式，来捕获路径中的目录名称
- `${1:/pascalcase}/`：捕获到的名称转化为 pascalcase

## 参考

- https://code.visualstudio.com/docs/editor/userdefinedsnippets#_transform-examples