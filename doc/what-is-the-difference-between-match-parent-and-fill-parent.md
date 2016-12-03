## match_parent 和 fill_parent 的区别?

> imhet 译自 [stackoverflow](http://stackoverflow.com/questions/5761960/what-is-the-difference-between-match-parent-and-fill-parent)

### 问题描述

我搞不明白 XML 的这两个属性 `match_parent` 和 `fill_parent` ，它们看起来都是一个意思，它们的区别是什么？

### 高票答案

它们是同一个意思（Android 2.2+）。请使用 `match_parent`。

下面是[官网](https://developer.android.com/reference/android/view/ViewGroup.LayoutParams.html)对它们的描述：

>  fill_parent ，意思是 view 想要跟它的父 view 一样大（减去填充）。该常量在 Android 2.2及以后版本中被废弃并且被重命名为 match_parent。
