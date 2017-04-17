# ProgessBar
## 一、概述
### ProgressBar,进度条，可以设置自定义的样式，其派生出很多控件，如SeekBar ,RatingBar ，还有ProgressDialog。

### 1基本用法
#### 最常用的用法，就是直接在XML布局文件中声明，制定style就可以了。其style有很多种，有横向长方形的，也有圆圈的。
    <ProgressBar
        android:max="100"
        android:progress="20"
        style="@android:style/Widget.ProgressBar.Horizontal"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/progressBar"
        android:layout_centerVertical="true"
        android:layout_centerHorizontal="true" />

#### 其中，我们比较关注的几个属性是：

#### android:max 制定进度条的最大值
#### android:progress 进度条的初始值，默认为0
#### style=:"@android:style/Widget.ProgressBar.Horizontal"指定了其样式

#### 以上的效果图如下：
![](http://upload-images.jianshu.io/upload_images/1876233-b339908b1f35c6fa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 当然，android中自带的央视有很多：
####style="@android:style/Widget.ProgressBar"灰色圆形
####style="@android:style/Widget.ProgressBar.Inverse"灰色圆形，旋转方向根上面的相反
####style="@android:style/Widget.ProgressBar.Large"大号的灰色圆形
####style="@android:style/Widget.ProgressBar.Large.Inverse"大号灰色圆形，带有 Inverse，

#### 所以旋转方向相反
#### style="@android:style/Widget.ProgressBar.Small"小号灰色圆形
#### style="@android:style/Widget.ProgressBar.Small.Inverse"小号灰色圆形，带有 Inverse，所以旋转方向相反
#### style="@android:style/Widget.ProgressBar.Horizontal"黄色进度，横向进度条

#### 其实，还有很多不同系列的。。。hole系列的等等：
#### 我们只需要知道几个关键的地方即可：
#### Large ，small，Inverse，Horizontal。
![](http://upload-images.jianshu.io/upload_images/1876233-dbdf18149ec67cf1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### Hole系列
![](http://upload-images.jianshu.io/upload_images/1876233-9ae23f68a17ca54b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#### Material系列：（需要API 21）
![](http://upload-images.jianshu.io/upload_images/1876233-68d7b23ebc384378.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### DeviceDefault系列：
#### 跟当前的API等级是一样的样式

### 2.自定义风格的进度条
#### 首先，在src/main/res/values/styles.xml中自定义自己的样式：
    <resources>

    <!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/colorPrimary</item>
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <item name="colorAccent">@color/colorAccent</item>
    </style>

    <style name="mprogressbar">
        <item name="android:indeterminateOnly">false</item>
        <item name="android:progressDrawable">@drawable/progressbar_diy</item><!-- progress_horizontal -->
        <item name="android:indeterminateDrawable">@android:drawable/progress_indeterminate_horizontal</item>
        <item name="android:minHeight">20dip</item>
        <item name="android:maxHeight">20dip</item>
    </style>

    </resources>

####这里，我定义的是mprogressbar。
#### 属性说明：从上到下：
#### indeterminateOnly 表示进度条的进度值是否确定。
#### progressDrawable 我们自定义的文件
#### indeterminateDrawable 进度条的进度值的风格，定义一些时间之类的。
#### minHeight 最小高
#### maxHeight 最大高
 
#### 我们主要是重写progressDrawable 指定的文件：
#### 这里的如下：
    <?xml version="1.0" encoding="utf-8"?>
    <layer-list xmlns:android="http://schemas.android.com/apk/res/android">

    <item android:id="@android:id/background">
    <shape>
        <corners android:radius="1dip" />
        <solid android:color="@android:color/darker_gray"/>
    </shape>
    </item>

    <item android:id="@android:id/secondaryProgress">
    <clip>
        <shape>
            <corners android:radius="1dip" />
            <gradient
                android:startColor="#80ffd300"
                android:centerColor="#80ffb600"
                android:centerY="0.75"
                android:endColor="#a0ffcb00"
                android:angle="270"
                />
        </shape>
    </clip>
    </item>

    <item android:id="@android:id/progress">
    <clip>
        <shape>
            <corners android:radius="1dip" />
            <solid android:color="@android:color/holo_orange_dark"/>
        </shape>
    </clip>
    </item>

    </layer-list>

#### 通过一个layer_list 根标签套住，三个item分别是：
- background：背景，主要是shape形状的设置
- secondaryProgress：第二进度条样式
- progress： 主进度条样式

### 3.竖立的进度条
#### 设计思路，在Draw的时候把其旋转即可。。。
    public class VerticalProgressBar extends ProgressBar {

    public VerticalProgressBar(Context context, AttributeSet attrs, int defStyle) {
        super(context, attrs, defStyle);
    }

    public VerticalProgressBar(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    public VerticalProgressBar(Context context) {
        super(context);
    }

    @Override
    protected void onSizeChanged(int w, int h, int oldw, int oldh) {
        super.onSizeChanged(h, w, oldh, oldw);
    }

    @Override
    protected synchronized void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);
    //        setMeasuredDimension(getMeasuredHeight(), getMeasuredWidth());
    }

    @Override
    protected synchronized void onDraw(Canvas canvas) {
        canvas.rotate(-90);
        canvas.translate(-getHeight(), 0);
        super.onDraw(canvas);
    }

    }

#### 使用直接全限定包名去用即可。。。
#### 其样式指定，DIY样式跟普通的进度条一样。
