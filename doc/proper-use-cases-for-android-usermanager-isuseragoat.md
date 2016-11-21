## 如何使用 UserManager.isUserAGoat()?

> imhet 译自 [stackoverflow](http://stackoverflow.com/questions/13375357/proper-use-cases-for-android-usermanager-isuseragoat)

### 问题描述

在查看 [Android 4.2](https://en.wikipedia.org/wiki/Android_version_history#Android_4.1.2F4.2_Jelly_Bean) 版本引入的新 API 时，我发现 [UserManager](https://developer.android.com/reference/android/os/UserManager.html) 这个类中有一个方法：

> public boolean isUserAGoat()

请问应该如何使用这个方法，以及什么时候应该使用它？

### 高票答案1

从[源码](https://android.googlesource.com/platform/frameworks/base/+/android-5.0.0_r6/core/java/android/os/UserManager.java#433)中可以看出来这个方法在 API 21 版本前一直返回的都是 false ：

```
/**
 * 用于检测调用这个方法的用户是否是远方穿越过来的山羊
 *
 * @返回 调用该方法的用户是否是山羊
 */
public boolean isUserAGoat() {
    return false;
}
```

看起来这个方法对开发者来说没有实际用处。也有人提到这个可能是一个彩蛋。

在 API 21 版本中该方法用于判断应用的包名是否是`com.coffeestainstudios.goatsimulator`：

```
/**
 * 用于检测调用这个方法的用户是否是远方穿越过来的山羊
 *
 * <p>在5.0版本中, 该方法能够使用山羊识别技术自动识别山羊了</p>
 *
 * @return 如果调用该方法的用户是否是山羊，那么返回结果是 true
 */
public boolean isUserAGoat() {
    return mContext.getPackageManager()
            .isPackageAvailable("com.coffeestainstudios.goatsimulator");
}

```


### 高票答案2

在 java 中下面代码编译器会给出警告：

```
while (1 == 2) { // 把 while 换成 if 编译器给出的警告会不一样
    System.out.println("Unreachable code");
}

```

但下面代码却是合法的：

```
while (isUserAGoat()) {
    System.out.println("Unreachable but determined at runtime, not at compile time");
}
```

我个人经常编写类似这种傻傻的方法，先以最快的方式把代码填好，完成调试的时候再找到它的所有引用，最后再实现该方法。

### 高票答案3

对高票答案2补充下说明。这种方法调用也可以用作一段虚拟代码，以便在某些特定迭代或特定递归调用中停止时在 IDE 中保持断点，例如：

![](https://i.stack.imgur.com/mW1Lf.png)

`isUserAGoat()`可用于替换虚拟变量声明，因为变量声明在 IDE 中经常会认为是一种警告，特别是在 Eclipse 中，这种警告的标记会挡住断点标记，让开启/关闭断点不好使。如果统一采用该方法，稍后可以使用脚本自动过滤掉它。

![](https://i.stack.imgur.com/MFIBa.png)

Google 的程序员都是重度的 Eclipse 用户，@djechlin的高票答案2和我这个补充的说明还是有一定道理的（至少对我来说是的）。
