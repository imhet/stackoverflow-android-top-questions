## 安装 Android SDK 时找不到 JDK

> imhet 译自 [stackoverflow](http://stackoverflow.com/questions/4382178/android-sdk-installation-doesnt-find-jdk)

### 问题描述

我想在 window 7 x64 系统上安装 Android SDK ，`jdk-6u23-windows-x64.exe` 已经安装了，但是安装引导进行不下去了，因为它找不到安装需要的 JDK。请问有解决方法么？

![](https://i.stack.imgur.com/pZjuL.jpg)

### 高票答案1

按 **Back** 返回上一步然后再按 **Next**，它就会找到 JDK 了。

### 高票答案2

安装好 JDK 后，像下面一样设置环境变量 `JAVA_HOME` ：

- `JAVA_HOME=C:/Program Files/Java/jdk1.8.0_11`
- `JRE_HOME=C:/Program Files/Java/jre8`
- `Path=%JAVA_HOME%;C:...`
