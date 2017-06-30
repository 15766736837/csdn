---
title: 百度地图SDK
---
[TOC]



## 百度地图SDK

### 申请APP Key

* 打开**百度开发者平台 **: http://lbsyun.baidu.com/index.php?title=androidsdk

![enter description here][1]

![enter description here][2]

* 获取SHA1

![enter description here][3]



![enter description here][4]



![enter description here][5]

* 发布版SHA1和开发版SHA1是可以一样的,这里我就都用开发版的SHA1了
![enter description here][6]
* 点击提交就可以获取当前应用的app Key了

![enter description here][7]



### 下载百度地图相关的SDK

* 地址 : http://lbsyun.baidu.com/index.php?title=androidsdk/sdkandev-download

![enter description here][8]

* 下载完后可以看到有以下文件

![enter description here][9]



### 给应用配置环境

* 全部放置到项目的libs目录下, 并给jar包添加依赖

![enter description here][10]

* 先在AndroidMainfest.xml中的application节点中配置信息：

```xml

<application
        ......>
        <meta-data
            android:name="com.baidu.lbsapi.API_KEY" <!--这里不需要修改-->
            android:value="开发者key" />	<!--这里需要我们之前申请的app Key-->
        ......
    </application>
```



* 在AndroidMainfest.xml中添加以下权限

```xml

 <uses-permission android:name="com.android.launcher.permission.READ_SETTINGS" />
    <!-- 这个权限用于进行网络定位 -->
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <!-- 这个权限用于访问GPS定位 -->
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <!-- 用于访问wifi网络信息，wifi信息会用于进行网络定位 -->
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <!-- 获取运营商信息，用于支持提供运营商信息相关的接口 -->
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <!-- 用于读取手机当前的状态 -->
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <!-- 写入扩展存储，向扩展卡写入数据，用于写入离线定位数据 -->
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <!-- 访问网络，网络定位需要上网 -->
    <uses-permission android:name="android.permission.INTERNET" />
```



* 在build.gradle文件中的android代码块中添加以下代码

```xml

 sourceSets {
        main {
            jniLibs.srcDirs = ['libs']
        }
    }
```



### 开始使用百度地图

![enter description here][11]

* 使用定位功能需要在AndroidManifest.xml中做以下配置

```xml

        <service
            android:name="com.baidu.location.f"
            android:enabled="true"
            android:process=":remote">
        </service>
```

* 布局代码

```xml

<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
                android:layout_width="match_parent"
                android:layout_height="match_parent" >

    <com.baidu.mapapi.map.MapView
        android:id="@+id/bmapView"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:clickable="true" />

    <LinearLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentRight="true"
        android:layout_marginTop="80dip"
        android:background="#88FFFFFF"
        android:minWidth="100dip"
        android:orientation="vertical"
        android:padding="2dp">

        <RadioGroup
            android:id="@+id/radioGroup"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:contentDescription="定位icon" >

            <RadioButton
                android:id="@+id/defaulticon"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:checked="true"
                android:text="默认图标" >
            </RadioButton>

            <RadioButton
                android:id="@+id/customicon"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="自定义图标" >
            </RadioButton>
        </RadioGroup>
    </LinearLayout>

    <Button
        android:id="@+id/button1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentRight="true"
        android:layout_alignParentTop="true"
        android:layout_marginRight="25dp"
        android:layout_marginTop="10dip" />

</RelativeLayout>
```



* MainActivity代码

```java

public class MainActivity extends AppCompatActivity {

    // 定位相关
    LocationClient mLocClient;
    public MyLocationListenner myListener = new MyLocationListenner();
    private MyLocationConfiguration.LocationMode mCurrentMode;
    BitmapDescriptor mCurrentMarker;
    private static final int accuracyCircleFillColor = 0xAAFFFF88;
    private static final int accuracyCircleStrokeColor = 0xAA00FF00;

    MapView mMapView;
    BaiduMap mBaiduMap;

    // UI相关
    RadioGroup.OnCheckedChangeListener radioButtonListener;
    Button requestLocButton;
    boolean isFirstLoc = true; // 是否首次定位

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        //在使用SDK各组件之前初始化context信息，传入ApplicationContext
        //注意该方法要再setContentView方法之前实现
        SDKInitializer.initialize(getApplicationContext());
        setContentView(R.layout.activity_main);
        //获取地图控件引用
        mMapView = (MapView) findViewById(R.id.bmapView);
        //定位
        location();
    }

    private void location() {
        requestLocButton = (Button) findViewById(R.id.button1);
        mCurrentMode = MyLocationConfiguration.LocationMode.NORMAL;
        requestLocButton.setText("普通");
        View.OnClickListener btnClickListener = new View.OnClickListener() {
            public void onClick(View v) {
                switch (mCurrentMode) {
                    case NORMAL:
                        requestLocButton.setText("跟随");
                        mCurrentMode = MyLocationConfiguration.LocationMode.FOLLOWING;
                        mBaiduMap
                                .setMyLocationConfigeration(new MyLocationConfiguration(
                                        mCurrentMode, true, mCurrentMarker));
                        break;
                    case COMPASS:
                        requestLocButton.setText("普通");
                        mCurrentMode = MyLocationConfiguration.LocationMode.NORMAL;
                        mBaiduMap
                                .setMyLocationConfigeration(new MyLocationConfiguration(
                                        mCurrentMode, true, mCurrentMarker));
                        break;
                    case FOLLOWING:
                        requestLocButton.setText("罗盘");
                        mCurrentMode = MyLocationConfiguration.LocationMode.COMPASS;
                        mBaiduMap
                                .setMyLocationConfigeration(new MyLocationConfiguration(
                                        mCurrentMode, true, mCurrentMarker));
                        break;
                    default:
                        break;
                }
            }
        };
        requestLocButton.setOnClickListener(btnClickListener);

        RadioGroup group = (RadioGroup) this.findViewById(R.id.radioGroup);
        radioButtonListener = new RadioGroup.OnCheckedChangeListener() {
            @Override
            public void onCheckedChanged(RadioGroup group, int checkedId) {
                if (checkedId == R.id.defaulticon) {
                    // 传入null则，恢复默认图标
                    mCurrentMarker = null;
                    mBaiduMap
                            .setMyLocationConfigeration(new MyLocationConfiguration(
                                    mCurrentMode, true, null));
                }
                if (checkedId == R.id.customicon) {
                    // 修改为自定义marker
                    mCurrentMarker = BitmapDescriptorFactory
                            .fromResource(R.mipmap.icon_geo);
                    mBaiduMap
                            .setMyLocationConfigeration(new MyLocationConfiguration(
                                    mCurrentMode, true, mCurrentMarker,
                                    accuracyCircleFillColor, accuracyCircleStrokeColor));
                }
            }
        };
        group.setOnCheckedChangeListener(radioButtonListener);

        // 地图初始化
        mMapView = (MapView) findViewById(R.id.bmapView);
        mBaiduMap = mMapView.getMap();
        // 开启定位图层
        mBaiduMap.setMyLocationEnabled(true);
        // 定位初始化
        mLocClient = new LocationClient(this);
        mLocClient.registerLocationListener(myListener);
        LocationClientOption option = new LocationClientOption();
        option.setOpenGps(true); // 打开gps
        option.setCoorType("bd09ll"); // 设置坐标类型
        option.setScanSpan(1000);
        mLocClient.setLocOption(option);
        mLocClient.start();
    }

    @Override
    protected void onDestroy() {
        // 退出时销毁定位
        mLocClient.stop();
        // 关闭定位图层
        mBaiduMap.setMyLocationEnabled(false);
        mMapView.onDestroy();
        mMapView = null;
        super.onDestroy();
    }

    @Override
    protected void onResume() {
        super.onResume();
        //在activity执行onResume时执行mMapView. onResume ()，实现地图生命周期管理
        mMapView.onResume();
    }

    @Override
    protected void onPause() {
        super.onPause();
        //在activity执行onPause时执行mMapView. onPause ()，实现地图生命周期管理
        mMapView.onPause();
    }

    /**
     * 定位SDK监听函数
     */
    public class MyLocationListenner implements BDLocationListener {

        @Override
        public void onReceiveLocation(BDLocation location) {
            // map view 销毁后不在处理新接收的位置
            if (location == null || mMapView == null) {
                return;
            }
            MyLocationData locData = new MyLocationData.Builder()
                    .accuracy(location.getRadius())
                    // 此处设置开发者获取到的方向信息，顺时针0-360
                    .direction(100).latitude(location.getLatitude())
                    .longitude(location.getLongitude()).build();
            mBaiduMap.setMyLocationData(locData);
            if (isFirstLoc) {
                isFirstLoc = false;
                LatLng ll = new LatLng(location.getLatitude(),
                        location.getLongitude());
                MapStatus.Builder builder = new MapStatus.Builder();
                builder.target(ll).zoom(18.0f);
                mBaiduMap.animateMapStatus(MapStatusUpdateFactory.newMapStatus(builder.build()));
            }
        }

        public void onReceivePoi(BDLocation poiLocation) {
        }
    }
}
```


  [1]: ./images/map1.jpg "map1"
  [2]: ./images/map2.jpg "map2"
  [3]: ./images/map3.jpg "map3"
  [4]: ./images/map4.jpg "map4"
  [5]: ./images/map5.jpg "map5"
  [6]: ./images/map6.jpg "map6"
  [7]: ./images/map7.jpg "map7"
  [8]: ./images/map8.jpg "map8"
  [9]: ./images/map9.jpg "map9"
  [10]: ./images/map10.jpg "map10"
  [11]: ./images/map1.gif "map1"