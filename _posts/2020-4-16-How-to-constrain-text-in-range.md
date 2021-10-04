---
title: 如何将 Android UI 的元素限定在某个范围内
category: 
- Android
tag: 
- Android
---

假设我要在屏幕上显示一段文本，要求它左侧对齐，且宽度不能超过屏幕左右两侧的 5% 。如何实现？

方法过于神奇，以至于我都不知道我当时是怎么想出来的。

<!-- more -->

假设要约束的文本 ID 为 `text`。将控制宽度的属性 `android:layout_width` 设为 `wrap_content`，`app:layout_constrainedWidth` 设为 `true`。

首先将布局设定为 `ConstrainLayout`，因为这个方法只有用 `ConstrainLayout` 里包含的组件才能实现。

接着新建两条 guideline，将它们的 `app:layout_constraintGuide_percent` 属性分别定为 0.05 和 0.95，方向 `android:orientation` 定为垂直 `vertical`。0.05 的那条设置 ID 为 `guideline_verticalS`，0.95 的那个为 `guideline_verticalE`。

然后新建一个 barrier，将 `app:barrierDirection` 设为 `start`，这是为了将方向设置为起始端。然后选择放入屏障的视图，即将 `app:constraint_referenced_ids` 设为 `guideline_verticalE, text`。

最后将 `text` 设为 Start 与 `guideline_verticalE` 的 Start 对齐，End 与 barrier 的 Start 对齐。

设置完成之后的 xml 属性大概是这样的：

``` xml
<androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent">

    <TextView
        android:id="@+id/text"
        android:layout_width="wrap_content"
        android:layout_height="0dp"
        app:layout_constrainedWidth="true"
        app:layout_constraintEnd_toStartOf="@id/text_barrier"
        app:layout_constraintStart_toStartOf="@id/guideline_verticalS"/>

    <androidx.constraintlayout.widget.Guideline
        android:id="@+id/guideline_verticalS"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        app:layout_constraintGuide_percent="0.05" />

    <androidx.constraintlayout.widget.Guideline
        android:id="@+id/guideline_verticalE"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        app:layout_constraintGuide_percent="0.95" />

    <androidx.constraintlayout.widget.Barrier
        android:id="@+id/text_barrier"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:barrierDirection="start"
        app:constraint_referenced_ids="guideline_verticalE, text" />
```

这样就可以了。
