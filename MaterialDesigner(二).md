---
title:  MaterialDesigner(二)
---
[TOC]
### FloatActionButton

* 浮动按钮


* android:src : 设置图片	
* android:backgroundTin : 设置浮动按钮的背景
* app:rippleColor : 设置点击背景,**注意 : 一定要设置clickable属性为true,不然无效**
* app:borderWidth : 应该设置为0，否则会在4.1会显示为正方形，而在5.0以后没有阴影效果
* app:fabSize : 设置浮动按钮的大小,只有normal和mini两个属性
* android:elevation : 设置阴影效果

![enter description here][1]

````xml
    <android.support.design.widget.FloatingActionButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="160dp"
        android:layout_marginTop="50dp"
        android:src="@mipmap/ic_brightness_medium_white_36dp"
        android:backgroundTint="#000"
        app:rippleColor="@color/colorAccent"
        app:borderWidth="0dp"
        app:fabSize="normal"
        android:elevation="10dp"
        android:clickable="true"/>
````



### ToolBar

* 可以把ToolBar看做是ActionBar增强版,ToolBar不但可以放置在顶部,还可以放置在任意的位置

![enter description here][2]

* app:logo : App的图标
* app:title : 大标题
* app:subtitle : 小标题
* app:navigationIcon
* inflateMenu(int resId) : 添加右边的菜单按钮
  * 在res文件夹中添加一个menu文件夹,存放菜单的xml布局
  * app:showAsAction : 设置item的位置
    * always : 显示在ToolBar上面
    * never : 折叠在菜单里面

![enter description here][3]

* xml代码

````xml
    <android.support.v7.widget.Toolbar
        android:id="@+id/tool_bar_tb"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="@color/colorAccent"
        app:logo="@mipmap/ic_launcher"
        app:title="大标题"
        app:subtitle="小标题"
        app:navigationIcon="@mipmap/ic_brightness_medium_white_36dp"
        />
````

* 菜单布局

````xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:app="http://schemas.android.com/apk/res-auto">
    <item
        android:icon="@mipmap/ic_launcher"
        app:showAsAction="always" />

    <item
        android:title="缩小"
        app:showAsAction="never" />

    <item
        android:id="@+id/setting"
        android:title="设置"
        app:showAsAction="never" />

    <item
        android:id="@+id/share"
        android:title="分享"
        app:showAsAction="never" />
</menu>
````



* java代码

````java
@Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.tool_bar_layout);
        initView();

    }

    private void initView() {
        mToolBarTb = (Toolbar) findViewById(R.id.tool_bar_tb);
        //设置ToolBar的菜单
        mToolBarTb.inflateMenu(R.menu.main);

      	//设置ToolBar的菜单的监听器
        mToolBarTb.setOnMenuItemClickListener(new Toolbar.OnMenuItemClickListener() {
            @Override
            public boolean onMenuItemClick(MenuItem item) {
                switch (item.getItemId()){
                    case R.id.sacleup:
                        break;
                    case R.id.sacledown:
                        break;
                    case R.id.share:
                        break;
                    case R.id.setting:

                        break;
                }  
              return false;
            }
        });
    }
````



### SnackBar

* 有着Toast的所有功能,还有着一些Toast没有的功能,比如可点击等



* 简单使用

![enter description here][4]

````
Snackbar.make(mActivityMain, "我是消息...", Snackbar.LENGTH_LONG)
	.show();
````



* 带有Action点击事件

![enter description here][5]

````java
Snackbar.make(mActivityMain, "我是消息...", Snackbar.LENGTH_LONG)
                .setAction("Action", new View.OnClickListener() {
                    @Override
                    public void onClick(View v) {
                        Toast.makeText(MainActivity.this, "我被点击了...", Toast.LENGTH_SHORT).show();
                    }
                })
                .show();
````



* 给SnackBar设置背景色和消息的字体颜色

![enter description here][6]

````java
		Snackbar snackbar = Snackbar.make(mActivityMain, "我是消息...", Snackbar.LENGTH_LONG);
        snackbar.setAction("Action", new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Toast.makeText(MainActivity.this, "我被点击了...", Toast.LENGTH_SHORT).show();
            }
        });
        snackbar.show();
        
        /**
         * 参数一:需要一个SnackBar
         * 参数二:SnackBar消息的颜色
         * 参数三:SnackBar的背景颜色
         */
        setSnackbarColor(snackbar, Color.WHITE, Color.YELLOW);
        
        public void setSnackbarColor(Snackbar s, int msgColor, int backgroundColor) {
        //获取SnackBar的布局
        View view = s.getView();
        if (view != null) {
          	//设置背景
            view.setBackgroundColor(backgroundColor);
            //获取到消息控件
          	TextView tv = (TextView) view.findViewById(R.id.snackbar_text);
          	//设置消息字体颜色  
          	tv.setTextColor(msgColor);
        }
    }
````



### CoordinatorLayout

* 作用:
  * 作为顶层部局
  * 调度协调子布局

![enter description here][7]

* 布局
  * 根布局使用了**CoordinatorLayout**
  * 包裹了两个子布局
    * AppBarLayout
      * CollapsingToolbarLayout : 实现折叠效果
      * ImageView
    * NestedScrollView
      * TextView

````xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.design.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <android.support.design.widget.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="250dp">
        <android.support.design.widget.CollapsingToolbarLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content">
            <ImageView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:src="@mipmap/material_flat"
                android:scaleType="fitXY"/>
        </android.support.design.widget.CollapsingToolbarLayout>
    </android.support.design.widget.AppBarLayout>
    
  <!--不加layout_behavior的话,两个子布局就会重叠在一起-->
    <android.support.v4.widget.NestedScrollView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior">
        <TextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="@string/content"/>
    </android.support.v4.widget.NestedScrollView>
</android.support.design.widget.CoordinatorLayout>

````

![enter description here][8]

````xml
<android.support.design.widget.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="250dp">
        <android.support.design.widget.CollapsingToolbarLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:layout_scrollFlags="scroll">	<!--这个属性就可以使文本滚动的时候图片也跟着一起滚动-->
            <ImageView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:src="@mipmap/material_flat"
                android:scaleType="fitXY"/>
        </android.support.design.widget.CollapsingToolbarLayout>
    </android.support.design.widget.AppBarLayout>
````

* ImageView是A控件,TextView是B控件
* scrollFlags : 有5个属性
  * scroll : B控件滚动时候A控件也会跟着B一起滚动
    * 以下属性都是需要配合着scroll属性才会有效果
    * scroll|enterAlways : 全部控件都滚动出去后,再次滚动进来时,A控件会先全部滚动进来先,再轮到B控件滚动进来
    * scroll|enterAlways|enterAlwaysCollapsed : 需要再配合android:minHeight属性才会有效果,指定一个高度,全部控件都滚动出去后,再次滚动进来时,A控件会先滚动出我们指定好的一个高度,再滚动完B控件,再去滚动剩余的A控件
    * scroll|exitUntilCollapsed : 也是需要跟android:minHeight属性一起使用,滚动出去时,A控件跟B控件一起滚动,A控件滚动到指定的高度后,就不再滚动



![enter description here][9]

````xml
 <android.support.design.widget.CollapsingToolbarLayout
            android:id="@+id/ctl"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:contentScrim="#1fe718"	//ToolBar的颜色
            app:layout_scrollFlags="scroll|exitUntilCollapsed"
            app:title="当然选择原谅她...
            app:expandedTitleTextAppearance="@style/TransparentText"
            app:collapsedTitleTextAppearance="@style/TransparentText">//修改ToolBar字体颜色

            <ImageView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:scaleType="fitXY"
                android:src="@mipmap/material_flat"
                app:layout_collapseMode="parallax"
                app:layout_collapseParallaxMultiplier="0.5"/>

            <android.support.v7.widget.Toolbar
                android:layout_width="match_parent"
                android:layout_height="?android:actionBarSize"
                app:layout_collapseMode="pin">
            </android.support.v7.widget.Toolbar>
        </android.support.design.widget.CollapsingToolbarLayout>
````

* ToolBar字体颜色的Style

````xml
<style name="TransparentText">
        <item name="android:textColor">#ff6646cf</item>
</style>
````





![enter description here][10]

* 要实现以上的效果,需要给FloatingActionButton和ImageView设置一个锚点(app:layout_anchor),并且需要给AppBarLayout设置id
* layout_anchorGravity : 可以设置控件的显示位置

````xml
	<android.support.design.widget.FloatingActionButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@mipmap/ic_brightness_medium_white_36dp"
        app:fabSize="mini"
        app:layout_anchor="@id/app_bar"
        android:layout_marginRight="40dp"
        app:layout_anchorGravity="right|bottom"/>

    <ImageView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@mipmap/ic_launcher"
        app:layout_anchor="@id/app_bar"
        app:layout_anchorGravity="bottom|center"/>
````



### Behavior

![enter description here][11]

* 布局代码

````xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.design.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <ImageView
        android:id="@+id/behavior_iv"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:src="@mipmap/ic_launcher"
        />

    <TextView
        android:id="@+id/behavior_tv"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World!"
        app:layout_behavior="com.lai.behavior_demo.EasyBehavior"/>
</android.support.design.widget.CoordinatorLayout>

````

* Activity代码

````java
public class MainActivity extends AppCompatActivity {

    private ImageView mBehaviorIv;
    int statusBarHeight1 = -1;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        initView();
        getStateHeight();
    }

    private void initView() {
        mBehaviorIv = (ImageView) findViewById(R.id.behavior_iv);

        mBehaviorIv.setOnTouchListener(new View.OnTouchListener() {
            @Override
            public boolean onTouch(View v, MotionEvent event) {
                switch (event.getAction()){
                    case MotionEvent.ACTION_MOVE:
                        int moveX = (int) event.getRawX();
                        int moveY = (int) (event.getRawY() - statusBarHeight1);

                        mBehaviorIv.setX(moveX);
                        mBehaviorIv.setY(moveY);
                        break;
                }
                return true;
            }
        });
    }

    private void getStateHeight() {
        //获取status_bar_height资源的ID
        int resourceId = getResources().getIdentifier("status_bar_height", "dimen", "android");
        if (resourceId > 0) {
            //根据资源ID获取响应的尺寸值
            statusBarHeight1 = getResources().getDimensionPixelSize(resourceId);
        }
    }
}
````

* 自定义Behavior代码

````java
public class EasyBehavior extends CoordinatorLayout.Behavior<TextView>{
    public EasyBehavior() {
        super();
    }

    public EasyBehavior(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    @Override
    public boolean layoutDependsOn(CoordinatorLayout parent, TextView child, View dependency) {
        return dependency.getId() == R.id.behavior_iv;
    }

    @Override
    public boolean onDependentViewChanged(CoordinatorLayout parent, TextView child, View dependency) {
        //TextView的位置跟着ImageView的改变而改变
      	child.setX(dependency.getX());
        child.setY(dependency.getY() + dependency.getHeight() + 10);
        return true;
    }
}

````



### Palette

* 获取取色器内部的颜色,颜色有可能为空,取的时候最好做非空判断
  * Vibrant （有活力）
  * Vibrant dark（有活力 暗色）
  * Vibrant light（有活力 亮色）
  * Muted （柔和）
  * Muted dark（柔和 暗色）
  * Muted light（柔和 亮色）

![enter description here][12]

* xml代码

````xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <android.support.v4.view.ViewPager
        android:id="@+id/vp"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
</RelativeLayout>

````

* Activity代码

````java
public class MainActivity extends AppCompatActivity implements ViewPager.OnPageChangeListener {

    private ViewPager mVp;
    private List<ImageView> mImageViews = new ArrayList<>();
    private HandlerThread mHandlerThread;
    private Handler mHandler;
    private Handler mMainHandler;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        //子线程,因为取图片颜色是比较耗时的,有可能会造成UI线程阻塞,所以该操作最好是在子线程执行
        mHandlerThread = new HandlerThread("worker");
        mHandlerThread.start();

        mHandler = new Handler(mHandlerThread.getLooper()) {
            @Override
            public void handleMessage(Message msg) {
                int arg1 = msg.arg1;
                getPhotoColor(arg1);
            }
        };

        //主线程
        mMainHandler = new Handler(){
            @Override
            public void handleMessage(Message msg) {
                setColor(msg.arg1);
            }
        };

        initView();
    }

    private void initView() {
        mVp = (ViewPager) findViewById(R.id.vp);
        MyPageAdapter adapter = new MyPageAdapter();

        mVp.addOnPageChangeListener(this);

        ImageView imageView1 = createImageView(R.mipmap.a);
        ImageView imageView2 = createImageView(R.mipmap.b);
        ImageView imageView3 = createImageView(R.mipmap.c);

        mImageViews.add(imageView1);
        mImageViews.add(imageView2);
        mImageViews.add(imageView3);

        mVp.setAdapter(adapter);
    }

    //获取ViewPage每一个Item的颜色
    private void getPhotoColor(int index) {
        Bitmap bitmap = null;
        switch (index) {
            case 0:
                bitmap = (Bitmap) getBitmap(R.mipmap.a);
                break;
            case 1:
                bitmap = (Bitmap) getBitmap(R.mipmap.b);
                break;
            case 2:
                bitmap = (Bitmap) getBitmap(R.mipmap.c);
                break;
        }

        Palette.from(bitmap).generate(new Palette.PaletteAsyncListener() {
            @Override
            public void onGenerated(Palette palette) {
                int color = 0;
                if (palette.getDarkMutedSwatch() != null) {
                    color = palette.getDarkMutedSwatch().getRgb();
                } else if (palette.getLightMutedSwatch() != null) {
                    color = palette.getLightMutedSwatch().getRgb();
                }
                mMainHandler.obtainMessage(0, color, 0).sendToTarget();
            }
        });
    }

    //获取item图片的Bitmap
    public Bitmap getBitmap(int resId) {
        Bitmap bitmap = BitmapFactory.decodeResource(getResources(), resId);
        return bitmap;
    }
    
    public void setColor(int color) {
        if (Build.VERSION.SDK_INT >= 21) {
            Window window = getWindow();
            //状态栏颜色
            window.setStatusBarColor(color);
            //导航栏颜色
            window.setNavigationBarColor(color);
        }
    }

    //创建ImageView
    public ImageView createImageView(int resId) {
        ImageView imageView = new ImageView(this);
        imageView.setScaleType(ImageView.ScaleType.FIT_XY);
        imageView.setImageResource(resId);
        return imageView;
    }

    @Override
    public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {

    }

    @Override
    public void onPageSelected(int position) {
        mHandler.obtainMessage(1, position, 0).sendToTarget();
    }

    @Override
    public void onPageScrollStateChanged(int state) {

    }

    //注销HandlerThread
    @Override
    protected void onDestroy() {
        super.onDestroy();
        mHandlerThread.quit();
    }
}
````

* ViewPage的Adapter

````java
    public class MyPageAdapter extends PagerAdapter {

        @Override
        public int getCount() {
            return mImageViews.size();
        }

        @Override
        public boolean isViewFromObject(View view, Object object) {
            return object == view;
        }

        @Override
        public ImageView instantiateItem(ViewGroup container, int position) {
            ImageView iv = mImageViews.get(position);
            container.addView(iv);
            return iv;
        }

        @Override
        public void destroyItem(ViewGroup container, int position, Object object) {
            container.removeView((View) object);
        }
    }
````


  [1]: ./images/FloatActionButton.gif "FloatActionButton"
  [2]: ./images/ToolBar.jpg "ToolBar"
  [3]: ./images/ToolBar.gif "ToolBar"
  [4]: ./images/SnackBar1.gif "SnackBar1"
  [5]: ./images/SnackBar2.gif "SnackBar2"
  [6]: ./images/SnackBar3.gif "SnackBar3"
  [7]: ./images/CoordinatorLayout1.gif "CoordinatorLayout1"
  [8]: ./images/CoordinatorLayout2.gif "CoordinatorLayout2"
  [9]: ./images/Coordinator3.gif "Coordinator3"
  [10]: ./images/CoordinatorLayout4.gif "CoordinatorLayout4"
  [11]: ./images/Behavior.gif "Behavior"
  [12]: ./images/Palette1.gif "Palette1"