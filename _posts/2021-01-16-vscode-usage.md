---
layout: post
title: 'VS code日常问题总结'
date: '2021-01-16'
categories: [VSCode]
---

### VS Code遇到的问题汇总

- 解决国内使用VsCode无法安装工具链
  - 在VsCode命令行输入:
  ```shell
  go env -w GO111MODULE=on
  go env -w GOPROXY=https://goproxy.io,direct
  ```
  - 再次点击`install all`就可以全部安装完成.

- Code Runner 运行程序时不自动切换到文件目录
```shell
// 设置属性
"code-runner.fileDirectoryAsCwd":true,
```
