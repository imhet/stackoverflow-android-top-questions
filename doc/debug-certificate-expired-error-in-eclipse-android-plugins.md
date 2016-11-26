## Eclipse 中开发 Android 时遇到 “Debug 签名过期”

> imhet 译自 [stackoverflow](http://stackoverflow.com/questions/2194808/debug-certificate-expired-error-in-eclipse-android-plugins)

### 问题描述

在 Eclipse 中使用 Android 插件开发项目时，我遇到了如下错误：

```
[2010-02-03 10:31:14 - androidVNC]Error generating final archive:
Debug certificate expired on 1/30/10 2:35 PM!

```

请问该如何处理？

### 高票答案

在如下目录下删除 android debug 签名文件：

- Linux 和 Mac OS X 中目录一般是 `~/.android/debug.keystore`
- 在 Window 中目录类似 `%USERPROFILE%/.android`

当你下次编译 debug 包时 Eclipse 会生成一个新的签名，你需要清理然后编译工程即可。


> Android SDK 会自动产生一个名称为 debug.keystore 的 debug 签名方便您的调试，但这个签名只有 365 天的有效期，删除旧的 debug.keystore 后才会有新的产生，一般来说你可以在 `Preferences - Android - Build - Default debug keystore.` 找到这个签名的位置。
