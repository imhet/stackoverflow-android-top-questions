## 如何让 TextView 中的文字在水平方向和垂直方向上居中显示?

> imhet 译自 [stackoverflow](http://stackoverflow.com/questions/432037/how-do-i-center-text-horizontally-and-vertically-in-a-textview-on-android)

### 问题描述

如题。

### 高票答案

如果你使用 XML 布局，可以

```
<TextView  
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:text="@string/**yourtextstring**"
/>
```

如果你用代码构造布局，可以使用 `.setGravity(Gravity.CENTER)` 。

如果是在 RelativeLayout 中可以使用

```
android:layout_centerInParent="true"

```
