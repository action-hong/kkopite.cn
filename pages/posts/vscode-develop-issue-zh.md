---
title: 开发 VS Code 插件碰到的一些问题
date: 2023-11-13T14:00:00.000+00:00
lang: zh
duration: 5 min
---

## github workflow 运行 VS Code 插件的测试，启动 @vscode/test-electron 报错

ubuntu 报如下错误：

```log
ozone_platform_x11.cc(240) Missing X server or $DISPLAY
env.cc(255) The platform failed to initialize.  Exiting.
The futex facility returned an unexpected error code.
Downloading VS Code [==============================] 100%Exit code:   SIGABRT
Failed to run tests
```

需要在 `xx.yml` 中加上该指令：

```yml
# https://github.com/prettier/prettier-vscode/blob/affc1e5b44fc6d0d9d4a957e327e0da57045bf56/.github/workflows/main.yml#L26-L44
- run: /usr/bin/Xvfb :99 -screen 0 1024x768x24 > /dev/null 2>&1 & echo "Started xvfb"
  shell: bash
  if: ${{ success() && matrix.os == 'ubuntu-latest' }}

- name: Test
  run: npm run test
  env:
    DISPLAY: ':99.0'
```

ubuntu 系统下需要启动 `Xvfb` 才行，或者使用 [setup-xvfb](https://github.com/coactions/setup-xvfb)

---

window 报如下错误：

```log
Downloaded VS Code into D:\a\zhlint-vscode\zhlint-vscode\.vscode-test\vscode-win32-x64-archive-1.84.2
Error: spawn UNKNOWN
    at ChildProcess.spawn (node:internal/child_process:421:11)
    at Object.spawn (node:child_process:761:9)
    at D:\a\zhlint-vscode\zhlint-vscode\client\node_modules\@vscode\test-electron\out\runTest.js:63:24
    at new Promise (<anonymous>)
    at innerRunTests (D:\a\zhlint-vscode\zhlint-vscode\client\node_modules\@vscode\test-electron\out\runTest.js:61:12)
    at runTests (D:\a\zhlint-vscode\zhlint-vscode\client\node_modules\@vscode\test-electron\out\runTest.js:42:12)
    at process.processTicksAndRejections (node:internal/process/task_queues:95:5)
    at async main (D:\a\zhlint-vscode\zhlint-vscode\client\out\test\runTest.js:19:9) {
  errno: -4094,
  code: 'UNKNOWN',
  syscall: 'spawn'
```

排除是 `@vscode/test-election` 的版本问题，[升级到最新版即可](https://github.com/microsoft/vscode-test/issues/234)
