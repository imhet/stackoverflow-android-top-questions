## Android 中px、dp、dip 和 sp 有什么区别?

> imhet 译自 [stackoverflow](http://stackoverflow.com/questions/2025282/what-is-the-difference-between-px-dp-dip-and-sp-on-android)

### 问题描述

Android 中px、dp、dip 和 sp 有什么区别?

### 高票答案

从[官方开发文档](https://developer.android.com/guide/topics/resources/more-resources.html#Dimension)中，可以看到：

>1. px 像素，对应于屏幕上的实际像素
2. in 英寸，基于屏幕的物理尺寸，1英寸 = 2.54厘米
3. mm 毫米，基于屏幕的物理尺寸
4. pt 点，基于屏幕的物理尺寸，相当于 1/72 英寸
5. dp 或 dip 与密度无关的像素单位，基于屏幕的物理尺寸的抽象单位。它相对于 160 dpi 的屏幕，在 160 dpi 的屏幕上 1dp 相当于 1px 。dp 与 pix 的比例会随着屏幕密度变化而变化，但不一定成正比。
6. sp 与缩放无关的像素单位，类似 dp ，不同之处在于它还会根据用户字体大小配置而缩放。开发中指定字体大小时建议使用 sp ，因为它会根据屏幕密度和用户字体配置而适配。



在[理解 Android 密度独立性](https://www.captechconsulting.com/blogs/understanding-density-independence-in-android)这篇文章中有两个比较有用的表可以回答你的问题：

| 屏幕密度级别 | 屏幕密度 | 物理尺寸 | 像素大小 |
| ---- | ---- | ---- | ---- |
| lpdi | 120 dpi | 0.5 x 0.5 in | 0.5 in * 120 dpi = 60x60 px |
| mdpi | 160 dpi | 0.5 x 0.5 in | 0.5 in * 160 dpi = 80x80 px  |
| hdpi | 240 dpi | 0.5 x 0.5 in | 0.5 in * 240 dpi = 120x120 px |
| xhdpi | 320 dpi | 0.5 x 0.5 in | 0.5 in * 320 dpi = 160x160 px |
| xxhdpi | 480 dpi | 0.5 x 0.5 in | 0.5 in * 480 dpi = 240x240 px |
| xxxhdpi | 640 dpi | 0.5 x 0.5 in | 0.5 in * 640 dpi = 320x320 px |

| 单位 | 描述 | 每英寸中含多少 | 密度无关 | 在每个屏幕上是否是同样大小  |
| ---- | ---- | ---- | ---- | ---- |
| px | 像素 | 很多 | 否 | 否 |
| in | 英寸 | 1 | 是 | 是 |
| mm | 毫米 | 25.4 | 是 | 是 |
| pt | 点 | 72 | 是 | 是 |
| dp | 密度无关像素 | ~160 | 是 | 是 |
| sp | 缩放无关像素 | ~160 | 是 | 是 |

更多信息可以参见 [Google 设计文档](https://material.google.com/layout/units-measurements.html)。
