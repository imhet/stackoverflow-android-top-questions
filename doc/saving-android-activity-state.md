## 如何保存 Activity 的状态？

> imhet 译自 [stackoverflow](http://stackoverflow.com/questions/151777/saving-android-activity-state)

### 问题描述

在使用 Android 开发过程中，我对如何保存一个应用的状态很迷惑，比如下面的代码：

```
package com.android.hello;

import android.app.Activity;
import android.os.Bundle;
import android.widget.TextView;

public class HelloAndroid extends Activity {
    /** Called when the activity is first created. */
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        mTextView = new TextView(this);

        if (savedInstanceState == null) {
            mTextView.setText("Welcome to HelloAndroid!");
        } else {
            mTextView.setText("Welcome back.");
        }

        setContentView(mTextView);
    }

    private TextView mTextView = null;
}
```

我觉得这个例子够简单了，但无论我怎么结束这个应用它给我的总是第一条信息。

我敢肯定是需要重写类似 `onPause` 这样的方法，但在官方文档中我找了很久也没找到明显的答案，期待你们的帮助，我将感激不尽。


### 高票答案

你需要重写 `onSaveInstanceState(Bundle savedInstanceState)` 并且需要写入一些应用相关的状态值到 `Bundle` 里去：

```
@Override
public void onSaveInstanceState(Bundle savedInstanceState) {
  super.onSaveInstanceState(savedInstanceState);

  // 保存 UI 状态变化到 savedInstanceState 变量里
  // 如果进程被杀或重启 bundle 会传递到 onCreate 方法中
  savedInstanceState.putBoolean("MyBoolean", true);
  savedInstanceState.putDouble("myDouble", 1.9);
  savedInstanceState.putInt("MyInt", 1);
  savedInstanceState.putString("MyString", "Welcome back to Android");
  // 等等
}

```

其中 Bundle 是存储键值对的重要方式，它会被传递到 `onCreate()` 和 `onRestoreInstanceState()` 中，在这两个方法里你可以像下面代码一样的把之前存储的值取出来：

```

@Override
public void onRestoreInstanceState(Bundle savedInstanceState) {
  super.onRestoreInstanceState(savedInstanceState);

  // 根据 savedInstanceState 恢复 UI 的状态
  // 该 bundle 同时也被传递到了 onCreate 方法中
  boolean myBoolean = savedInstanceState.getBoolean("MyBoolean");
  double myDouble = savedInstanceState.getDouble("myDouble");
  int myInt = savedInstanceState.getInt("MyInt");
  String myString = savedInstanceState.getString("MyString");
}

```

通常你会使用该技术来存储你的应用里面的实例值（被选中的信息，未保存的文本，等等）。更多信息请参看[官方文档](https://developer.android.com/reference/android/app/Activity.html#onSaveInstanceState(android.os.Bundle))。
