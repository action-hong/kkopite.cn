---
title: 开发 uni-app 碰到得一些问题
date: 2024-04-17T23:00:00.000+00:00
lang: zh
duration: 10 min
---

## 请在 manifest 中配置该插件，重新制作包括该原生插件的自定义运行基座

使用原生插件运行时碰到如下错误：

```log
[JS Framework] 当前运行的基座不包含原生插件[PrinterModule]，请在manifest中配置该插件，重新制作包括该原生插件的自定义运行基座
```

需要自己去制作[自定义基座](https://uniapp.helpyougo.com/web/userpage/documents?id=1&type=1)：

1. 运行-运行到手机或模拟器-制作自定义调试基座
![运行-运行到手机或模拟器-制作自定义调试基座](/images/uni-app/1.png)
2. android 选择公共测试证书 (如果只是测试) **实际生成环境需使用正确得证书**
![android证书](/images/uni-app/2.png)
3. ios 填写相关证书 (测试时只测试 android 得话可跳过)
4. 点击传统打包，等待打包
![打包](/images/uni-app/3.png)
5. 运行-运行到手机或模拟器-运行到 Android-App 基座，选择 “使用自定义基座运行”
![运行](/images/uni-app/4.png)

### 参考链接

- https://uniapp.helpyougo.com/web/userpage/documents?id=1&type=1
- https://uniapp.dcloud.net.cn/tutorial/run/run-app.html#playground
- https://blog.csdn.net/hu104160112/article/details/122574455?spm=1001.2101.3001.6650.2&utm_medium=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~Rate-2.pc_relevant_antiscanv2&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2~default~CTRLIST~Rate-2.pc_relevant_antiscanv2&utm_relevant_index=5