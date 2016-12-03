## Android 开发中的 Context 是指什么?

> imhet 译自 [stackoverflow](http://stackoverflow.com/questions/3572463/what-is-context-on-android)

### 问题描述

Android 开发中，Context 类究竟是什么，如何使用？

我在官网看到了关于它的介绍，但我还是没理解清楚。

### 高票答案

顾名思义，它是应用或对象的当前状态的上下文。它可以让新创建的对象了解发生了什么。通常来说，你可以调用它获取相关程序（activity 或 application）另一部分的信息。

通过调用 `getApplicationContext()` 、` getContext()` 、 `getBaseContext()` 或者 `this`(在 activity 里面时) 你可以获取 context。

使用 context 的一些场景：

- 创建新的对象：比如创建新的 view 、adapter 或 listeners :
  ```
  TextView tv = new TextView(getContext());
  ListAdapter adapter = new SimpleCursorAdapter(getApplicationContext(), ...);

  ```
- 访问标准的公共资源：比如获取像 LAYOUT_INFLATER_SERVICE 这样的 Service ，还有 SharedPreferences ：
  ```
  context.getSystemService(LAYOUT_INFLATER_SERVICE)
  getApplicationContext().getSharedPreferences(*name*, *mode*);

  ```
- 获取一些常见组件：比如 content provider 、 broadcast 或 intent ：
  ```
  getApplicationContext().getContentResolver().query(uri, ...);

  ```
