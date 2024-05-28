---
title: 在 Android 里如何设置 imageView 的图片自适应
date: 2020/5/18
updated: 2020/5/18
category: 
- Mist
- Android
tag: 
- Android
---

假设我要在屏幕上显示一张图片，要求它左侧对齐、自适应图片宽高，且宽度不能超过屏幕左右两侧的 5% 。如何实现？

与上篇关于 Android 的 blog 有着差不多的范围限定需求，但是有更多的杂七杂八的需求了。

<!-- more -->

图片是需要一个 imageView 来加载的，所以往布局里放一个 imageView。

假设要约束的图片 ID 为 `image`。将控制宽度的属性 `android:layout_width` 和 `android:layout_height` 都设为 `wrap_content`，`app:layout_constrainedWidth` 设为 `true`。

将 `android:scaleType` 设为 `fitStart`。这个属性类似于其他控件的 `android:layout_gravity`，这里设置成 `fitStart`，类似于 `Gravity.LEFT`。

将 `android:adjustViewBounds` 设为 `true`。这个属性是用来管理按比例缩放的，`true` 就是开启。

两条 guideline，将它们的 `app:layout_constraintGuide_percent` 属性分别定为 0.05 和 0.95，方向 `android:orientation` 定为垂直 `vertical`。0.05 的那条设置 ID 为 `guideline_verticalS`，0.95 的那个为 `guideline_verticalE`。

一个 barrier，将 `app:barrierDirection` 设为 `start`，将 `app:constraint_referenced_ids` 设为 `guideline_verticalE, image`。

最后将 `image` 设为 Start 与 `guideline_verticalE` 的 Start 对齐，End 与 barrier 的 Start 对齐。

这样就可以了。

## 参考链接

[Gravity  \|  Android Developers](https://developer.android.google.cn/reference/android/view/Gravity)

[imageView  \|  Android Developers](https://developer.android.google.cn/reference/android/widget/ImageView)

[使用 ConstraintLayout 构建自适应界面  \|  Android 开发者  \|  Android Developers](https://developer.android.google.cn/training/constraint-layout)
