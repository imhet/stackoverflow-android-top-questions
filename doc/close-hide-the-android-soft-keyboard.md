## 如何关闭或隐藏 Android 中的软键盘？

> imhe 译自 [stackoverflow](http://stackoverflow.com/questions/1109022/close-hide-the-android-soft-keyboard)

### 问题描述

我写了个布局，其中有一个 EditText 和一个 Button。在 EditText 中编辑完并点击 Button 后我希望能够隐藏虚拟键盘，请问有没简单的实现方法？

### 高票答案1

使用 [InputMethodManager](https://developer.android.com/reference/android/view/inputmethod/InputMethodManager.html) 的 [hideSoftInputFromWindow](https://developer.android.com/reference/android/view/inputmethod/InputMethodManager.html#hideSoftInputFromWindow%28android.os.IBinder,%20int%29) 方法，并传递包含你的 view 的窗口的句柄。

```
View view = this.getCurrentFocus();
if (view != null) {  
    InputMethodManager imm = (InputMethodManager)getSystemService(Context.INPUT_METHOD_SERVICE);
    imm.hideSoftInputFromWindow(view.getWindowToken(), 0);
}

```

这种强制隐藏软键盘的方法适用于所有情况。但某些情况下你可以在`hideSoftInputFromWindow`方法的第2个参数中传入 `InputMethodManager.HIDE_IMPLICIT_ONLY` 确保你只希望当用户没有明确强制它出现（通过按住菜单）时隐藏软键盘。

### 高票答案2

下面这种方式也可以实现隐藏软键盘。

```
InputMethodManager imm = (InputMethodManager) getSystemService(Activity.INPUT_METHOD_SERVICE);
imm.toggleSoftInput(InputMethodManager.HIDE_IMPLICIT_ONLY, 0);

```

### 译者补充

显示/隐藏软键盘的方法也可以参考[这里](https://github.com/imhet/TinyUtil/blob/master/lib/src/main/java/im/het/tiny/util/ViewUtil.java)。
