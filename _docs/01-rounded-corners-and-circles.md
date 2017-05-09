---
docid: rounded-corners-and-circles
title: 圆角和圆圈
layout: docs
permalink: /docs/rounded-corners-and-circles.html
prev: scaling.html
next: using-controllerbuilder.html
---

应用经常需要让图片有圆角或者直接变成圆形来显得更加好看，Drawee 可以轻松支持圆角显示，并且显示圆角时，并不复制和修改Bitmap对象，那样太耗费内存。

## 圆角

圆角实际有2种呈现方式:

1. 圆圈 - 设置`roundAsCircle`为true
2. 圆角 - 设置`roundedCornerRadius`

置圆角时，支持4个角不同的半径。XML中无法配置，但可在Java代码中配置。

### 设置圆角

可使用以下两种方式:

1. 默认使用一个 shader 绘制圆角，但是仅仅占位图和所要显示的图有圆角效果。失败示意图和重下载示意图无圆角效果，且这种圆角方式不支持动画。
2. 叠加一个`solid color`来绘制圆角。但是背景需要固定成指定的颜色。
    在XML中指定 `roundWithOverlayColor`, 或者通过调用`setOverlayColor`来完成此设定。

### XML中配置

`SimpleDraweeView` 支持如下几种圆角配置:

```xml
<com.facebook.drawee.view.SimpleDraweeView
   ...
   fresco:roundedCornerRadius="5dp"
   fresco:roundBottomLeft="false"
   fresco:roundBottomRight="false"
   fresco:roundWithOverlayColor="@color/blue"
   fresco:roundingBorderWidth="1dp"
   fresco:roundingBorderColor="@color/red"
```

### 代码中配置

在创建 [DraweeHierarchy](using-drawees-code.html) 时，可以给 `GenericDraweeHierarchyBuilder` 指定一个[RoundingParams](../javadoc/reference/com/facebook/drawee/generic/RoundingParams.html) 用来绘制圆角效果。

```java
RoundingParams roundingParams = RoundingParams.fromCornersRadius(7f);
// alternatively use fromCornersRadii or asCircle
roundingParams.setOverlayColor(R.color.green);
genericDraweeHierarchyBuilder
    .setRoundingParams(roundingParams);
```

你也可以在运行时，改变圆角效果

```java
RoundingParams roundingParams = RoundingParams.fromCornersRadius(5f);
roundingParams.setBorder(getResources().getColor(R.color.red), 1.0f);
roundingParams.setRoundAsCircle(true);
mSimpleDraweeView.getHierarchy().setRoundingParams(roundingParams);
```

### 说明

当使用`BITMAP_ONLY`（默认）模式时的限制：

- 并非所有的图片分支部分都可以实现圆角，目前只有占位图片和实际图片可以实现圆角，我们正在努力为背景图片实现圆角功能。
- 只有`BitmapDrawable` 和 `ColorDrawable`类的图片可以实现圆角。我们目前不支持包括`NinePatchDrawable`和 `ShapeDrawable`在内的其他类型图片。（无论他们是在XML或是程序中声明的）
- 动画不能被圆角。
- 由于Android的`BitmapShader`的限制，当一个图片不能覆盖全部的View的时候，边缘部分会被重复显示，而非留白。对这种情况可以使用不同的缩放类型（比如centerCrop）来保证图片覆盖了全部的View。

`OVERLAY_COLOR`模式没有上述限制，但由于这个模式使用在图片上覆盖一个纯色图层的方式来模拟圆角效果，因此只有在图标背景是静止的并且与图层同色的情况下才能获得较好的效果。

Drawee 内部实现了一个`CLIPPING`模式。但由于有些`Canvas`的实现并不支持路径剪裁（Path Clipping），这个模式被禁用了且不对外开放。并且由于路径剪裁不支持反锯齿，会导致圆角的边缘呈现像素化的效果。

总之，如果生成临时bitmap的方法，所有的上述问题都可以避免。但是这个方法并不被支持因为这会导致很严重的内存问题。

综上所述，在 Android 中实现圆角效果，没有一个绝对好的方案，你必须在上述的方案中进行选择。
