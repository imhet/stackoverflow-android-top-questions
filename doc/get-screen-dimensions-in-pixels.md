## 如何获取屏幕尺寸

> imhet 译自 [stackoverflow](http://stackoverflow.com/questions/1016896/get-screen-dimensions-in-pixels)

### 问题描述

在一个 Activity 中如何获取屏幕宽高？

### 高票答案1

可以使用 [getSize](https://developer.android.com/reference/android/view/Display.html#getSize%28android.graphics.Point%29) 方法来获取屏幕尺寸(单位为像素)：

```
Display display = getWindowManager().getDefaultDisplay();
Point size = new Point();
display.getSize(size);
int width = size.x;
int height = size.y;

```

如果需要在非 Activity 的场景中获取屏幕尺寸，可以通过 `WINDOW_SERVICE` 来获取默认的 `Display` 然后使用 getSize 来获取 ：

```

WindowManager wm = (WindowManager) context.getSystemService(Context.WINDOW_SERVICE);
Display display = wm.getDefaultDisplay();

```


`getSize` 这个方法是在 API 13 时引入的，如果需要兼容之前的版本，可以用 `getWidth` 和 `getHeight` 来获取屏幕宽度和高度：

```

Display display = getWindowManager().getDefaultDisplay();
int width = display.getWidth();  // API 13以后该方法被废弃
int height = display.getHeight();  // API 13以后该方法被废弃

```

还有一种跟版本无关的方法是使用 [DisplayMetrics](https://developer.android.com/reference/android/util/DisplayMetrics.html):


```
DisplayMetrics metrics = new DisplayMetrics();
getWindowManager().getDefaultDisplay().getMetrics(metrics);
int width = metrics.widthPixels ;
int height = metrics.heightPixels ;

```

### 高票答案2

考虑到屏幕包含状态栏和菜单栏，我这里给出一个回答：

```
WindowManager w = activity.getWindowManager();
Display d = w.getDefaultDisplay();
DisplayMetrics metrics = new DisplayMetrics();
d.getMetrics(metrics);
widthPixels = metrics.widthPixels;
heightPixels = metrics.heightPixels;
// 包含状态栏和菜单栏的屏幕尺寸
if (Build.VERSION.SDK_INT >= 14 && Build.VERSION.SDK_INT < 17)
try {
    widthPixels = (Integer) Display.class.getMethod("getRawWidth").invoke(d);
    heightPixels = (Integer) Display.class.getMethod("getRawHeight").invoke(d);
} catch (Exception ignored) {
}
// 包含状态栏和菜单栏的屏幕尺寸
if (Build.VERSION.SDK_INT >= 17)
try {
    Point realSize = new Point();
    Display.class.getMethod("getRealSize", Point.class).invoke(d, realSize);
    widthPixels = realSize.x;
    heightPixels = realSize.y;
} catch (Exception ignored) {
}

```

### 高票答案3

最简单的方法：

```
int screenHeight = getResources().getDisplayMetrics().heightPixels;
int screenWidth = getResources().getDisplayMetrics().widthPixels;

```
