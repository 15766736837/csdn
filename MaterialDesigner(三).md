---
title:  MaterialDesigner(三)
---
[TOC]

### ActivityOptionsCompat 过渡动画

![enter description here][1]

* makeCustomAnimation (Context context, int enterResId, int exitResId)
  * 需要自定义两个动画效果

````java
ActivityOptionsCompat compat = ActivityOptionsCompat.makeCustomAnimation(this, R.anim.in, R.anim.out);
Intent intent = new Intent(this, MyActivity.class);
ActivityCompat.startActivity(this, intent, compat.toBundle());
````

*  makeScaleUpAnimation (View source, int startX,int startY, int width,int height)  
*  缩放动画,可以指定位置开始缩放,这里我是在图片中间开始进行缩放的

````java
ActivityOptionsCompat compat = ActivityOptionsCompat.makeScaleUpAnimation(
	view, view.getWidth() / 2, 	view.getHeight() / 2, 0, 0);
Intent intent = new Intent(this, MyActivity.class);
ActivityCompat.startActivity(this, intent, compat.toBundle());
````

*  makeThumbnailScaleUpAnimation(View source, Bitmap thumbnail, int startX, int startY)
*  默认从图片的左上角开始缩放,可以指定偏移量

````java
Bitmap bitmap = BitmapFactory.decodeResource(getResources(), R.mipmap.a4);
        ActivityOptionsCompat compat =
                ActivityOptionsCompat.makeThumbnailScaleUpAnimation(view, bitmap, -500, 500);
        Intent intent = new Intent(this, MyActivity.class);
        ActivityCompat.startActivity(this, intent, compat.toBundle());
````

* makeSceneTransitionAnimation : 共享元素动画
  * 条件一,需要布局1和布局2,有共同的元素
  * 给这两个共同的元素设置android:transitionName属性,和id

````java
        ActivityOptionsCompat compat = ActivityOptionsCompat.makeSceneTransitionAnimation(this,
                Pair.create(findViewById(R.id.img1), "noe"),
                Pair.create(findViewById(R.id.img2), "two"));
        Intent intent = new Intent(this, MyActivity.class);
        ActivityCompat.startActivity(this, intent, compat.toBundle());
````



### Scene(场景动画)

* google在旧版本的API中提供了View Animation与Propery Animator两套动画系统,可是这两套动画系统都是要程序员自己编写大量的动画代码,但是在5.0版本后,程序员只需要把开始动画前的布局和结束时的布局写出来就可以了,不用再去写大量的动画代码了,这个动画就是**transition**
* 场景动画 : 我们可以把需要实现动画效果的布局或者部分布局叫做场景,我们在切换场景的时候只需要告诉它实现什么效果的动画就可以.
* Scene.getSceneForLayout(ViewGroup sceneRoot, int layoutId, Context context) : 创建一个场景
  * sceneRoot : 场景的位置,就是说我这个场景的作用域,我这里是使用了MainActivity的跟布局
  * layoutId : 场景的布局
  * context : 上下文对象
* TransitionManager.beginDelayedTransition(Viewgroup group)  :  延迟场景变化，TransitionManager就会根据场景变化自动执行动画
* TransitionManager.go(Scene scene, Transition transition)  : 切换到指定的场景
  * scene : 场景对象
  * transition : 以什么动画切换场景 

![enter description here][2]

````java
/**
*	两个场景对应两个布局,还给布局中的ImageView设置了transitionName的属性
*/
public class MainActivity extends AppCompatActivity {

    private RelativeLayout mActivityMain;
    private Scene mScene1;
    private Scene mScene2;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mActivityMain = (RelativeLayout) findViewById(R.id.activity_main);

        if (Build.VERSION.SDK_INT >= 21) {
            //创建两个场景
            mScene1 = Scene.getSceneForLayout(mActivityMain, R.layout.scene1_layout, this);
            mScene2 = Scene.getSceneForLayout(mActivityMain, R.layout.scene2_layout, this);

            //一开始Activity就切换到场景1,为了到场景二后可以切换回场景二
            TransitionManager.go(mScene1, new ChangeBounds());
        }
    }

    //切换到场景二
    public void click(View view) {
        if (Build.VERSION.SDK_INT >= 21 && mScene1 != null) {
            TransitionManager.go(mScene2, new ChangeBounds());
        }
    }

    //切换到场景1
    public void scene2(View view) {
        if (Build.VERSION.SDK_INT >= 21 && mScene1 != null) {
            TransitionManager.go(mScene1, new ChangeBounds());
        }
    }
}
````



### beginDelayedTransition

* beginDelayedTransition(ViewGroup sceneRoot, Transition transition) : beginDelayedTransition不会直接就执行动画效果,需要它监听的ViewGroup对象发生了变化才会执行动画效果

![enter description here][3]

* 布局代码

````xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/content"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <Button
        android:onClick="click"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="点我"/>

    <TextView
        android:id="@+id/tv"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textSize="25sp"
        android:text="Hello World!"/>
</LinearLayout>

````

* java代码

````java
public class MainActivity extends AppCompatActivity {

    private TextView mTv;
    private LinearLayout mContent;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initView();
    }

    private void initView() {
        mTv = (TextView) findViewById(R.id.tv);
        mContent = (LinearLayout) findViewById(R.id.content);
    }

    public void click(View view) {
        int visibility = mTv.getVisibility();

        if (Build.VERSION.SDK_INT >= 21) {
            //开始监视ViewGroup,对象一旦发生变化,就会执行动画
            TransitionManager.beginDelayedTransition(mContent, new Slide(Gravity.RIGHT));
        }
		//让其发生变化从而执行动画
        mTv.setVisibility(visibility == View.VISIBLE ? View.GONE : View.VISIBLE);
    }
}

````



#### explose

* 类似爆炸的动画效果

![enter description here][4]

````java
 @RequiresApi(api = Build.VERSION_CODES.LOLLIPOP)
    @Override
    public void onItemClick(int Position) {
        //创建一个爆炸的动画
        Explode explode = new Explode();
        //延时时间
        explode.setDuration(1000);
        //监听ViewGroup,一旦变化就开始动画
        TransitionManager.beginDelayedTransition(mRlv, explode);
        //删除RecyclerView里面的数据,让其发生变化,从而启动动画
        mMyAdapter.removeDatas();
    }
````



### Transition 与 Activity

* Transition 与 Activity的跳转动画有以下几个
  * setEnterTransition Activity进入的动画
  * setExitTransition  Activity退出的动画
  * setReturnTransition Activity返回的动画
  * setReenterTransition Activity重入的动画
* 这个Demo是一个Activity退出时的一个动画效果

![enter description here][5]

````java
public class MainActivity extends AppCompatActivity {

    @RequiresApi(api = Build.VERSION_CODES.LOLLIPOP)
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        //想要使用transition动画需要配置
        Slide explode = new Slide(Gravity.RIGHT);
        getWindow().setExitTransition(explode);
        getWindow().requestFeature(Window.FEATURE_CONTENT_TRANSITIONS);

        setContentView(R.layout.activity_and_transition);
    }

    public void explose(View view) {
        Intent intent = new Intent(this, Main2Activity.class);
        if (Build.VERSION.SDK_INT >= 21)
            startActivity(intent, ActivityOptions.makeSceneTransitionAnimation(this).toBundle());
    }
}
````



### 水波纹动画

*  ViewAnimationUtils.createCircularReveal(View view,int centerX,int centerY,float startRadius, float endRadius)
  * view 用来显示圆形动画的控件
  * centerX 圆心的坐标X轴
  * centerY 圆心的坐标Y轴
  * startRadius 动画开始圆形的半径
  * endRadius 动画结束时圆形的半径

![enter description here][6]

````java
public class MainActivity extends AppCompatActivity {

    private FrameLayout mFl;
    private RelativeLayout mActivityMain;
    private int mWidth;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        //获取屏幕宽度
        WindowManager wm = this.getWindowManager();
        mWidth = wm.getDefaultDisplay().getWidth();

      	//获取布局的控件
        initView();
    }

    private void initView() {
        mFl = (FrameLayout) findViewById(R.id.fl);
        mActivityMain = (RelativeLayout) findViewById(R.id.activity_main);
    }

    @RequiresApi(api = Build.VERSION_CODES.LOLLIPOP)
    public void click(View view) {
        Animator anim = ViewAnimationUtils.createCircularReveal(
                mFl, (int)(mWidth / 2), (int)(mFl.getMeasuredHeight() / 2),
                0, mWidth);
        mFl.setBackgroundColor(Color.RED);//波纹的颜色
        anim.setDuration(2000);	//动画执行时间
        anim.setInterpolator(new AccelerateDecelerateInterpolator());
        anim.start();	//开启动画
    }
}
````



* 共享动画+水波纹动画

![enter description here][7]

* Activity1的代码,做了一个共享动画,需要在两个共享元素的控件上(两个圆)加上transitionName属性

````java
public class RippleActivity extends AppCompatActivity{
    @RequiresApi(api = Build.VERSION_CODES.KITKAT)
    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.ripper_activity_layout);
    }

    public void click(View view) {
        ActivityOptionsCompat compat = ActivityOptionsCompat.makeSceneTransitionAnimation(this,
                findViewById(R.id.circle1), "circle");
        Intent intent = new Intent(this, CircleActivity2.class);
        ActivityCompat.startActivity(this, intent, compat.toBundle());
    }
}
````

* Activity2的代码

````java
public class CircleActivity2 extends AppCompatActivity{
    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ChangeBounds changeBounds = new ChangeBounds();
        //进场动画结束后,再执行水波纹动画
        changeBounds.addListener(new Transition.TransitionListener() {
            @Override
            public void onTransitionStart(Transition transition) {
            }

            @Override
            public void onTransitionEnd(Transition transition) {
                //水波纹动画
                show();
            }

            @Override
            public void onTransitionCancel(Transition transition) {

            }

            @Override
            public void onTransitionPause(Transition transition) {

            }

            @Override
            public void onTransitionResume(Transition transition) {

            }
        });


        getWindow().requestFeature(Window.FEATURE_CONTENT_TRANSITIONS);
        getWindow().setSharedElementEnterTransition(changeBounds);
        setContentView(R.layout.circle_activit_layout2);
    }

    public void show(){
        Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);

        //位置
        int width = toolbar.getMeasuredWidth() / 2;
        int height = toolbar.getMeasuredHeight() / 2;

        int max = Math.max(width, height);

        Animator anim = ViewAnimationUtils.createCircularReveal(toolbar, width, height, 0, max);
        toolbar.setBackgroundColor(getResources().getColor(R.color.colorPrimary));
        anim.setDuration(1000);
        anim.start();
    }
}
````


  [1]: ./images/ActivityOptionsCompat%20.gif "ActivityOptionsCompat"
  [2]: ./images/Scene1.gif "Scene1"
  [3]: ./images/beginDelayedTransition.gif "beginDelayedTransition"
  [4]: ./images/explose.gif "explose"
  [5]: ./images/Transition%20.gif "Transition"
  [6]: ./images/createCircularReveal.gif "createCircularReveal"
  [7]: ./images/createCircularReveal1.gif "createCircularReveal1"