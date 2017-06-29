---
title: RecyclerView
---
[TOC]

## RecyclerView

* RecyclerView 是google在android 5.0新推出的一个控件, RecyclerView可以完全替代掉ListView,GridView,它的灵活性与可替代性比Listview更好,但是RecyclerView使用起来也是相对于比较麻烦的,很多东西都是需要我们自定义的,比如Item的点击事件也是要自定义



* 添加RecyclerView的依赖

```xml
compile 'com.android.support:recyclerview-v7:25.3.1'
```



## RecyclerView简单使用

![enter description here][1]

* 布局代码

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <android.support.v7.widget.Toolbar
        android:id="@+id/toolbar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:title="RecyclerViewDemo"
        android:background="@color/colorPrimary"/>


    <android.support.v7.widget.RecyclerView
        android:id="@+id/rv"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
</LinearLayout>

```



* RecyclerView的Adapter类,需要继承RecyclerView.Adapter,并实现他的三个方法
  * getItemCount : 可以看做是ListView的getCount方法,用来确定我要显示多少个item
  * onCreateViewHolder :  其实就是ListView的getView中的两段逻辑 
    * 1 创建view对象（将xml转为view）   
    * 2 找到view对象里面的一些控件，对其进行一些展示的设置
  * onBindViewHolder : 对ViewHolder里面的控件设置数据

```java
public class MyRecyclerViewAdapter extends RecyclerView.Adapter {
    private List<String> mDatas;

  	//接收要显示的数据
    public void setDatas(List<String> datas) {
        mDatas = datas;
    }

    @Override
    public RecyclerView.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.item_layout, parent, false);
        MyRecyclerViewHolder myViewHolder = new MyRecyclerViewHolder(view);
        return myViewHolder;
    }

    @Override
    public void onBindViewHolder(RecyclerView.ViewHolder holder, final int position) {
        ((MyRecyclerViewHolder) holder).setTextView(mDatas.get(position));
    }

    @Override
    public int getItemCount() {
        return mDatas != null ? mDatas.size() : 0;
    }
}

```



* ViewHolder类的代码

```java
public class MyRecyclerViewHolder extends RecyclerView.ViewHolder{

    private CardView mCardview;
    private TextView mTextView;

    public MyRecyclerViewHolder(View itemView) {
        super(itemView);
        mTextView = (TextView) itemView.findViewById(R.id.tv);
        mCardview = (CardView) itemView.findViewById(card_view);
    }

    public void setTextView(String text){
        if (mTextView != null){
            mTextView.setText(text);
        }
    }
}
```



* 在Activity中获取到RecyclerView,并设置Adapter,把要显示的数据传递给Adapter
* 以下代码都是跟ListView一样的逻辑,唯一不一样的是我们必须给RecyclerView设置layoutManage,不然的话是显示不出数据的,有以下三种layoutManager
  * GridLayoutManager(Context context, int spanCount, int orientation,boolean reverseLayout) : 
    * 显示出来的效果跟网格布局是一样的
    * context : 上下文对象
    * spanCount : 如果排列方向是水平方向,就表示行数,否则就是列数
    * reverseLayout : 为true就是把数据反向显示
  * StaggeredGridLayoutManager : 瀑布流
  * LinearLayoutManager : 效果跟ListView是一样的

```java
mRv = (RecyclerView) findViewById(R.id.rv);
LinearLayoutManager linearLayoutManager = new LinearLayoutManager(MainActivity.this);
mRv.setLayoutManager(manager);	
mAdapter = new MyRecyclerViewAdapter();	//创建RecyclerView的Adapter
mAdapter.setDatas(mDatas);				//给Adapter设置数据
mRv.setAdapter(mAdapter);
```
## 自定义Item点击事件
![enter description here][2]
* 自定义Item点击事件需要用到接口回调的方式


* 在Adapter类中的onBindViewHolder方法里添加以下代码
* 添加一个方法用来接收接口的示例

```java
	private OnRecyclerViewItemOnClickListener mListener;
	
	public void setListener(OnRecyclerViewItemOnClickListener listener) {
        mListener = listener;
    }

	@Override
    public void onBindViewHolder(RecyclerView.ViewHolder holder, final int position) {
        ((MyRecyclerViewHolder) holder).setTextView(mDatas.get(position));

        //设置点击事件
        holder.itemView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mListener != null) {
                    mListener.onItemClick(position);
                }
            }
        });
    }
```

* 接口回调的代码

```java
public interface OnRecyclerViewItemOnClickListener {
  	//  position是Item的位置
  	void onItemClick(int position);
}
```

* 最后需要在使用Item点击事件的类中实现OnRecyclerViewItemOnClickListener接口,然后调用Adapter中的setListener方法

## 设置添加/删除数据的动画效果
![enter description here][3]
* 这个是RecyclerView自带的添加/删除的动画效果,只需要在添加数据后加上以下代码就可以了

```java
//添加动画, position是添加数据的位置
mAdapter.notifyItemInserted(int position);
//删除动画, position是删除数据的位置
mAdapter.notifyItemRemoved(int position);
```

* 如果是要自定义动画效果需要给RecyclerView调用setItemAnimator方法,并实现SimpleItemAnimator的方法,具体的动画这里就不演示了

```java
        mRv.setItemAnimator(new SimpleItemAnimator() {
            @Override
            public boolean animateRemove(RecyclerView.ViewHolder holder) {
                return false;
            }

            @Override
            public boolean animateAdd(RecyclerView.ViewHolder holder) {
                return false;
            }

            @Override
            public boolean animateMove(RecyclerView.ViewHolder holder, 
                                       int fromX, int fromY, int toX, int toY) {
                return false;
            }

            @Override
            public boolean animateChange(RecyclerView.ViewHolder oldHolder, 
                                         RecyclerView.ViewHolder newHolder, 
                                         int fromLeft, int fromTop, int toLeft, int toTop) {
                return false;
            }

            @Override
            public void runPendingAnimations() {

            }

            @Override
            public void endAnimation(RecyclerView.ViewHolder item) {

            }

            @Override
            public void endAnimations() {

            }

            @Override
            public boolean isRunning() {
                return false;
            }
        });

```

* 这里给大家介绍一个比较多人用的RecyclerView动画的第三方框架
* 地址 : https://github.com/wasabeef/recyclerview-animators

## RecyclerView拖拽/滑动删除效果
![enter description here][4]
* 需要实现拖拽和滑动的效果要用到ItemTouchHelper类,ItemTouchHelper是一个处理RecyclerView的滑动删除和拖拽的辅助类
* 下面就直接附上代码了

```java
	mItemTouchHelper = new ItemTouchHelper(new ItemTouchHelper.Callback() {
            @Override
            public int getMovementFlags(RecyclerView recyclerView, 
                                        RecyclerView.ViewHolder viewHolder) {
                //拖拽和滑动的标志
                int dragFlags = 0
                int swipeFlags = 0;
                //判断是否是网格布局还是瀑布流
                if (recyclerView.getLayoutManager() instanceof StaggeredGridLayoutManager || 							recyclerView.getLayoutManager() instanceof GridLayoutManager) {
                    //网格布局有四个方向
                    dragFlags = ItemTouchHelper.UP |
                            ItemTouchHelper.DOWN |
                            ItemTouchHelper.LEFT |
                            ItemTouchHelper.RIGHT;
                } else if (recyclerView.getLayoutManager() instanceof LinearLayoutManager) {
                    //线性布局有两个方向
                    dragFlags = ItemTouchHelper.UP | ItemTouchHelper.DOWN;
                    swipeFlags = ItemTouchHelper.START | ItemTouchHelper.END;
                }
                return makeMovementFlags(dragFlags, swipeFlags);
            }

            @Override
            public boolean onMove(RecyclerView recyclerView, 
                                 RecyclerView.ViewHolder viewHolder, RecyclerView.ViewHolder target) {
                //长按会回调这个方法
                int from=viewHolder.getAdapterPosition();
                int to=target.getAdapterPosition();

                String moveItem = mDatas.get(from);
                mDatas.remove(from);
                mDatas.add(to,moveItem);//交换数据链表中数据的位置

                mAdapter.notifyItemMoved(from,to);//更新适配器中item的位置
                return true;
            }

            @Override
            public void onSwiped(RecyclerView.ViewHolder viewHolder, int direction) {
                TextView tv = (TextView) viewHolder.itemView.findViewById(R.id.tv);
                String data = tv.getText().toString();
                mDatas.remove(data);
                //这里处理滑动删除
                mAdapter.notifyDataSetChanged();
            }

            @Override
            public boolean isLongPressDragEnabled() {
                //返回true则为所有item都设置可以拖拽
                return true;
            }

            //当item拖拽开始时调用
            @Override
            public void onSelectedChanged(RecyclerView.ViewHolder viewHolder, int actionState) {
                super.onSelectedChanged(viewHolder, actionState);
                if(actionState==ItemTouchHelper.ACTION_STATE_DRAG){
                    viewHolder.itemView.setBackgroundColor(Color.TRANSPARENT);//拖拽时设置背景色为透明
                }
            }

            //当item拖拽完成时调用
            @Override
            public void clearView(RecyclerView recyclerView, RecyclerView.ViewHolder viewHolder) {
                super.clearView(recyclerView, viewHolder);
                viewHolder.itemView.setBackgroundColor(Color.WHITE);//拖拽停止时设置背景色为白色
            }

            //当item视图变化时调用
            @Override
            public void onChildDraw(Canvas c, RecyclerView recyclerView, 
                                    RecyclerView.ViewHolder viewHolder, float dX, 
                                    float dY, int actionState, boolean isCurrentlyActive) {
                super.onChildDraw(c, recyclerView, viewHolder, dX, dY, 
                                  actionState, isCurrentlyActive);
                //根据item滑动偏移的值修改item透明度。screenwidth是我提前获得的屏幕宽度
                viewHolder.itemView.setAlpha(1-Math.abs(dX)/width);
            }
        });
```

* 添加完以上代码后,我们还需要然ItemTouchHelper跟RecyclerView绑定在一起

```java
mItemTouchHelper.attachToRecyclerView(mRv);
```


  [1]: ./images/RecyclerView1.gif "RecyclerView1"
  [2]: ./images/RecyclerView2.gif "RecyclerView2"
  [3]: ./images/RecyclerView3.gif "RecyclerView3"
  [4]: ./images/RecyclerView4.gif "RecyclerView4"