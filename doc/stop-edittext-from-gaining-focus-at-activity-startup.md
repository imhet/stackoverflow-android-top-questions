## 如何让 EditText 在 Activity 启动时不获得焦点

> imhet 译自 [stackoverflow](http://stackoverflow.com/questions/1555109/stop-edittext-from-gaining-focus-at-activity-startup)

### 问题描述

我的应用里有一个 Activity ，其中包含一个 EditText 和 一个 ListView ，当 Activity 启动时，EditText 会立即获得焦点，我尝试了下面的这句：

```
EditText.setSelected(false);

```

但无济于事，请问 Activity 启动时如何阻止 EditText 获得焦点？

### 高票答案1

在 EditText 的父组件（比如 LinearLayout ）中添加如下两个属性：

```

android:focusable="true"

android:focusableInTouchMode="true"

```

如果没有父组件的话可以添加一个隐藏的 LinearLayout ，并加上上面的两个属性:

```

<LinearLayout

android:focusable="true"

android:focusableInTouchMode="true"

android:layout_width="0px"

android:layout_height="0px" />

```

### 高票答案2

你是不想让它获得焦点？还是不想因为焦点在 EditText 上导致显示虚拟键盘？我觉得 EditText 在启动时获取到了焦点不是一个问题，但用户没有主动请求 EditText 的焦点的情况下却导致虚拟键盘的打开，这肯定是一个问题。

这实际上是虚拟键盘的问题，参见 `[<activity> element](https://developer.android.com/guide/topics/manifest/activity-element.html)` 相关文档。

设置 `android:windowSoftInputMode="stateHidden"` 可以在进入 Activity 后隐藏虚拟键盘，或者 设置 `android:windowSoftInputMode="stateUnchanged"` 让虚拟键盘的状态不改变（也即是说，当 Activity 转至前台时保留软键盘最后所处的任何状态，无论是可见还是隐藏。）。
