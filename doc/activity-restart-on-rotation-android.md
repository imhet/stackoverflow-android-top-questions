## 处理Android 设备翻转后 Activity 的重启

> imhet 译自 [stackoverflow](http://stackoverflow.com/questions/456211/activity-restart-on-rotation-android)

### 问题描述

在我开发的 Android 应用中，翻转设备后我创建的 Activity 重新启动了（表现为 onCreate 被调用了）。可能这种情况是正常的，但我在 `onCreate` 中有很多初始化的工作，我该怎么处理：

1. 把所有初始化的工作放在另外一个方法里这样翻转时它们不会丢失？
2. 或者确保 `onCreate` 不要再调用了并且调整下布局？
3. 或者把应用限制为竖屏状态，这样 `onCreate` 就不会被调用了？

### 高票答案

在你的 AndroidManifest.xml 里面的 Activity 加上:

```
android:configChanges="keyboardHidden|orientation|screenSize"

```
同时在 Activity 中重写 `onConfigurationChanged` 方法并调用 `setContentView` 强制UI 重写布局。

```
@Override
public void onConfigurationChanged(Configuration newConfig) {
  super.onConfigurationChanged(newConfig);
  setContentView(R.layout.myLayout);
}

```

更多信息参考官网文档[处理运行时更改](https://developer.android.com/guide/topics/resources/runtime-changes.html#HandlingTheChange)。
