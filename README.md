---
title: MaterialDesigner
---
[TOC]

## MaterialDesign

### 修改应用内部的一些样式和颜色

![enter description here][1]

* 修改状态栏颜色:

````java
//xml中修改
//AndroidManifest -> android:theme="@style/AppTheme" -> 
<item name="colorPrimaryDark">@color/colorPrimaryDark</item><!--状态栏颜色-->
  
/**
* java代码中修改
* 注意:这里需要API21及以上的才可以修改
*/
Window window = getWindow();
window.setStatusBarColor(0xffffff);
````

* 修改导航栏颜色

````java
//xml中修改
//AndroidManifest -> android:theme="@style/AppTheme"
<item name="android:navigationBarColor">@color/colorAccent</item>
  
//
/**
* java代码中修改
* 注意:这里需要API21及以上的才可以修改
*/
Window window = getWindow();
window.setNavigationBarColor(0x000000);
````

### MaterialDesigner新增了Z轴

![enter description here][2]

* 在xml中修改自定义的drawable颜色

````xml
android:tint="@android:color/holo_green_dark"
````

* 效果图

![enter description here][3]

* xml代码

````xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <ImageView
        android:id="@+id/circle1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:background="@drawable/circle"
        android:layout_margin="50dp"/>
    <ImageView
        android:id="@+id/circle2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@drawable/circle"
        android:tint="@android:color/holo_green_dark"<!--修改drawable的颜色-->
        android:layout_margin="65dp"/>
</RelativeLayout>
````

* java代码

````java
public class MainActivity extends AppCompatActivity implements View.OnTouchListener {

    private ImageView mCircle1;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mCircle1 = (ImageView) findViewById(R.id.circle1);
        mCircle1.setOnTouchListener(this);
    }

    @Override
    public boolean onTouch(View v, MotionEvent event) {
      //数值越大,控件的Z轴就越高  
      switch (event.getAction()) {
            case MotionEvent.ACTION_UP:
                if (Build.VERSION.SDK_INT >= 21)
                    mCircle1.setTranslationZ(1);
                break;
            case MotionEvent.ACTION_DOWN:
                if (Build.VERSION.SDK_INT >= 21)
                    mCircle1.setTranslationZ(0);
                break;
        }
        return true;
    }
}

````



### 水波纹效果

![enter description here][4]

* 在res下新建 layout-v21包
* 把实现了水波纹效果控件的布局复制到layout-v21
* 在要实现水波纹的控件上添加以下代码

````xml
//无边界
android:background="?android:selectableItemBackgroundBorderless"
//有边界
android:background="?android:selectableItemBackground"
````



### 在android中 ? 和 @ 的区别

*  @ 代表引用资源,直接就是获取到值
*  ? 代表引用主题属性,属性的值是在另外的地方设置的


* @

![enter description here][5]

![enter description here][6]


* ?

![enter description here][7]



![enter description here][8]



### CardView

* 先找到CardView的aar包

![enter description here][9]


![enter description here][10]

* cardElevation :阴影效果
* cardCornerRadius : 圆角效果
* cardBackgroundColor :背景颜色

````xml
 <android.support.v7.widget.CardView
        android:layout_width="300dp"
        android:layout_height="50dp"
        app:cardElevation="10dp"
        app:cardCornerRadius="20dp"
        app:cardBackgroundColor="#ff0"
        android:layout_gravity="center"
        android:layout_marginTop="50dp">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center"
            android:textSize="22sp"
            android:text="CardView"/>
    </android.support.v7.widget.CardView>
````



### TextInputLayout

* app:hintAnimationEnabled : 是否开启提示的动画效果,默认开启
* app:counterEnabled : 对输入的字进行计数
* app:counterMaxLength : 设置总的字个数
* android:hint : 设置提示的内容
* android:textColorHint : 设置输入框里面字体的颜色
* setError : 设置错误提示,在java代码中设置

![enter description here][11]

````java
//设置错误信息
mPwdEt.setError("不能为空!!!");
````



* xml代码

````xml
<android.support.design.widget.TextInputLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="50dp"
        android:textColorHint="#0ff"><!--修改内部提示语的颜色-->

        <EditText
            android:id="@+id/user_et"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="账号: "/>
    </android.support.design.widget.TextInputLayout>

    <android.support.design.widget.TextInputLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="5dp"
        app:hintTextAppearance="@style/MyStyle">

        <EditText
            android:id="@+id/pwd_et"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="密码: "/>
    </android.support.design.widget.TextInputLayout>
````

* style代码

````xml
    <style name="MyStyle" parent="@android:style/TextAppearance">
        <item name="android:textColor">#AA00FF</item><!--外部提示字体的颜色-->
        <item name="android:textSize">22sp</item><!--外部提示字体的大小-->
    </style>

    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/colorPrimary</item>
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <item name="colorAccent">#0000FF</item><!--这里可以修改边框和边框外边提示字体的颜色-->
    </style>
````


  [1]: ./images/1.jpg "1"
  [2]: ./images/2.jpg "2"
  [3]: ./images/Z%E8%BD%B4.gif "Z轴"
  [4]: ./images/%E6%B0%B4%E6%B3%A2%E7%BA%B9_1.gif "水波纹"
  [5]: ./images/@%E7%9A%84%E5%90%AB%E4%B9%892.jpg "@的含义2"
  [6]: ./images/@%E7%9A%84%E5%90%AB%E4%B9%891.jpg "@的含义1"
  [7]: ./images/%E9%97%AE%E5%8F%B7%E7%9A%84%E5%90%AB%E4%B9%891.jpg "问号的含义1"
  [8]: ./images/%E9%97%AE%E5%8F%B7%E7%9A%84%E5%90%AB%E4%B9%892.jpg "问号的含义2"
  [9]: ./images/card_aar.jpg "card_aar"
  [10]: ./images/cardView1.jpg "cardView1"
  [11]: ./images/textInputLayout.gif "textInputLayout"
  [12]: ./images/1498613407995.jpg