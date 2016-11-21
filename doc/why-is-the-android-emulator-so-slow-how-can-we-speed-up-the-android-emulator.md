## Android 模拟器为什么那么慢，应如何加速

> imhet 译自 [stackoverflow](http://stackoverflow.com/questions/1554099/why-is-the-android-emulator-so-slow-how-can-we-speed-up-the-android-emulator)

### 问题描述

如题。

### 高票答案

[ADT 9.0.0](https://developer.android.com/studio/tools/sdk/eclipse-adt.html)及之后的版本可以让你保存 AVD（模拟器）的状态，你可以很快的启动你的模拟器。创建 AVD 时可你可以开启该特性，也可以创建之后再配置模拟器。

当然把模拟器的内存设得更高些也会加快模拟器的启动速度，比如我设置了 1024 。

具体设置步骤参考下面截图：

> 创建新的 AVD 时勾选 snapshot 选项。

![](https://i.stack.imgur.com/ITRGe.png)

> 从 snapshot 启动模拟器。

![](https://i.stack.imgur.com/Ml9Cd.png)

其它加速模拟器的方法请参考[加速你的 Android 模拟器](https://jolicode.com/blog/speed-up-your-android-emulator)。

### 其它答案

建议使用其它手段调试应用会比模拟器更快：

- 使用 [Genymotion](https://www.genymotion.com/)
- 使用真机
- 使用 [Android x86](http://www.android-x86.org/)
