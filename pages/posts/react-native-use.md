---
title: 开发 React Native 碰到的问题
date: 2024-09-19T16:00:00.000+00:00
lang: zh
duration: 10 min
---

## 尺寸太长的图片在 android 下显示模糊

笔者在开发一个 React Native 项目时，需要渲染一张本地图片，在 iOS 上显示正常，但是在 android 上显示模糊。图片的尺寸为 `1702 * 4143`

谷歌了一下，发现已经是好几年的 [bug](https://github.com/facebook/react-native/issues/21301) 了，但一直没解决。

可行的解决方法只能是：

- 使用 `react-native-fast-image`
- 使用 `WebView` 来渲染

由于项目里只有一处地方的图片有这个问题，所有笔者直接将图片裁成两张图片来显示，也能绕过这个问题快速解决

