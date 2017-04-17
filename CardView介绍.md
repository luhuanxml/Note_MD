# CardView

## CardView概述

### CardView 是android 5.0提出来的，一款卡片式控件，可以设置阴影、圆角等属性。

```
<attr name="cardBackgroundColor" format="color" /><!-- 背景色 -->
<attr name="cardCornerRadius" format="dimension" /><!-- 边缘弧度数 -->
<attr name="cardElevation" format="dimension" /> <!-- 高度 -->
<attr name="cardMaxElevation" format="dimension" /> <!-- 最大高度 -->
<!-- 设置内边距 设置内边距，v21+的版本和之前的版本仍旧具有一样的计算方式-->
<attr name="cardUseCompatPadding" format="boolean" />  
<!-- 在v20和之前的版本中添加内边距，这个属性是为了防止卡片内容和边角的重叠 -->
<attr name="cardPreventCornerOverlap" format="boolean" />

<!-- 下面是卡片边界距离内部的距离-->
<attr name="contentPadding" format="dimension" />
<attr name="contentPaddingLeft" format="dimension" />
<attr name="contentPaddingRight" format="dimension" />
<attr name="contentPaddingTop" format="dimension" />
<attr name="contentPaddingBottom" format="dimension" />
```
## 排版技巧
###### CardView本质上属于FrameLayout，而CardView通常包含了较多的内容元素，为了方便的排版布局中的各个元素，一般借助其他基本容器，比如使用RelativeLayout或者LinearLayout作为CardView的唯一Child。
## 阴影Padding
###### 在Android Lollipop之前的系统，CardView会自动添加一些额外的padding空间来绘制阴影部分，这导致了以Lollipop为分界线的不同系统上的CardView的尺寸大小不同。为了解决这个问题，有两种方法：
- 第一种 ，使用不同API版本的dimension资源适配（也就是借助values和values-21文件夹中不同的dimens.xm文件）
- 第二种，就是使用```cardUseCompatPadding```属性，设置为true（默认是false），让CardView在不同系统中使用相同的padding值。
## 圆角覆盖
这也是解决系统兼容的问题，在pre-Lollipop平台（API 21版本之前）上，CardView不会裁剪内容元素以满足圆角需求，而是使用添加padding的替代方案，从而使元素不会覆盖CardView的圆角。而控件这个行为的属性是```cardPreventCornerOverlap```，默认值为true。

> 下图左边为false  右边为true
> 显然，默认为true时自动添加padding的方式不可取，所以需要设置该属性为false。需要注意的是在lollipop及以上的版本的系统中没有任何影响，除非cardUseCompatPadding的值为true。

![](http://ocq7gtgqu.bkt.clouddn.com/CardView-samples-02.png)

## Ripple效果

Cards一般都是可以点击的，为此我们使用了foreground属性并使用系统selectableItemBackground值，同时设置clickable属性为true。从而达到在Lollipop及以上版本系统中实现点击时涟漪效果（ripple）

![](http://ocq7gtgqu.bkt.clouddn.com/CardView-samples-03.gif)

## lift-on-touch 效果的实现
> 在res/drawable 目录下建立一个 lift-on-touch.xml 文件
```
<?xml version="1.0" encoding="utf-8"?>
<!-- animate the translationZ property of a view when pressed -->
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:state_enabled="true"
        android:state_pressed="true">
        <set>
            <objectAnimator
                android:duration="@android:integer/config_shortAnimTime"
                android:propertyName="translationZ"
                android:valueTo="6dp"
                android:valueType="floatType"/>
        </set>
    </item>
    <item>
        <set>
            <objectAnimator
                android:duration="@android:integer/config_shortAnimTime"
                android:propertyName="translationZ"
                android:valueTo="0"
                android:valueType="floatType"/>
        </set>
    </item>
</selector>
```

