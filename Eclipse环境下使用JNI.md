---
title: Eclipse环境下使用JNI
---

### 准备工作:

* 需要准备好android-ndk-r9d-windows-x86的包
* 关联NDK

![enter description here][1]

![enter description here][2]



#### 在JAVA代码中调用C代码

* 在java代码中创建一个本地方法使用(**`native`**)修饰
* 使用一下操作自动生成jni文件夹和.c文件跟Android.mk

![enter description here][3]

![enter description here][4]

* 自动生成了Android.mk文件

```java

LOCAL_PATH := $(call my-dir)

include $(CLEAR_VARS)

LOCAL_MODULE    := add	//这个就是我们输入的模块名
LOCAL_SRC_FILES := add.c //这个生成出来后默认是.cpp,根据需要改变

include $(BUILD_SHARED_LIBRARY)
```

* 自动生成了add.cpp文件,根据需要可以改名为add.c文件,这里面是写我们C代码的

```c

#include <jni.h>
#include <stdio.h>
#include <stdlib.h>

JNIEXPORT jstring JNICALL Java_com_lai_hello_MainActivity_add(JNIEnv* env, jobject obj){
	//C里面定义字符串的格式	
  	char* arr = "hello fromC!!!";
	//jstring     (*NewStringUTF)(JNIEnv*, const char*);
  	//返回字符串到java代码中
	return (*env)->NewStringUTF(env, arr);	//写法1
  //return (*(*env)).NewStringUTF(env, arr);//写法2
}

```

* java代码

```java

package com.lai.hello;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Toast;

public class MainActivity extends Activity {
	static{
		System.loadLibrary("add");
	}
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }
  	//本地方法,提供给我们C代码调用的,然后把执行后的结果返回给我java代码中
    public native String add();
   
    public void click(View view){
    	Toast.makeText(this,  add(), 0).show();
    }
}

```

* 获取到C代码中调用java的本地方法的格式
* 进入到该项目的 src 目录下,按住shift+右键,打开命令行,输入 javah 包名 类名 然后在src目录下会生成一个.h文件,复制里面的方法到C代码中就可以了,.h文件可以删除

![enter description here][5]

#### 在C代码中调用java代码

* **注意:** 不管是在java调用C还是C调用java,都需要在java中配置

```java

    static{
    	System.loadLibrary("alipay");	//模块名
    }
```

* 获取要调用的Java方法签名(**注意**)
  * 进入classes目录下,打开命令行,输入 javap -s 包名加上类名
* java代码

```java

package com.lai.alipay;

import android.app.Activity;
import android.app.AlertDialog;
import android.os.Bundle;
import android.view.View;
import android.widget.EditText;

public class MainActivity extends Activity {

	private EditText user_name_et;
    private EditText pwd_et;
    private EditText money;
    private AlertDialog mDialog;
    
    static{
    	System.loadLibrary("alipay");
    }
    
    //给C代码调用的方法
    public native void setAlipay(String user, String pwdm, float money);
    
    //显示对话框,提供给C代码调用
    public void showDialog(String message){
        AlertDialog.Builder builder = builder = new AlertDialog.Builder(this);
        builder.setTitle("֧支付");
        builder .setMessage(message);
        mDialog = builder.show();
    
    }

    public void dismissDialog(){
        mDialog.dismiss();
    }
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        
        user_name_et = (EditText) findViewById(R.id.user_name_et);
        pwd_et = (EditText) findViewById(R.id.pwd_et);
        money = (EditText) findViewById(R.id.money);
    }
    
    public void click(View view){
        String user = user_name_et.getText().toString();
        String pwd = pwd_et.getText().toString();
        String money = this.money.getText().toString();
        
        /**
         * 传String类型的参数过去,一定要先把代码的编码换成UTF-8
         */
        setAlipay(user, pwd, Float.valueOf(money));
    }
}

```

* c代码

```c

#include <jni.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

//把String转成Char
char* JString2CStr(JNIEnv* env, jstring jstr) {
	 char* rtn = NULL;
	 jclass clsstring = (*env)->FindClass(env, "java/lang/String");
	 jstring strencode = (*env)->NewStringUTF(env,"GB2312");
	 jmethodID mid = (*env)->GetMethodID(env, clsstring, "getBytes", "(Ljava/lang/String;)[B");
	 jbyteArray barr = (jbyteArray)(*env)->CallObjectMethod(env, jstr, mid, strencode); // String .getByte("GB2312");
	 jsize alen = (*env)->GetArrayLength(env, barr);
	 jbyte* ba = (*env)->GetByteArrayElements(env, barr, JNI_FALSE);
	 if(alen > 0) {
		rtn = (char*)malloc(alen+1); //"\0"
		memcpy(rtn, ba, alen);
		rtn[alen]=0;
	 }
	 (*env)->ReleaseByteArrayElements(env, barr, ba,0);
	 return rtn;
}

JNIEXPORT void JNICALL Java_com_lai_alipay_MainActivity_setAlipay
  (JNIEnv* env, jobject obj, jstring user, jstring pwd, jfloat money){
	//把String转成char
	char* userChar = JString2CStr(env, user);
	char* pwdChar = JString2CStr(env, pwd);

	//判断账号密码是否一致
	extern int    strcmp(const char *, const char *);
	int isUser = strcmp(userChar, "abc");
	int isPwd = strcmp(pwdChar, "123");

	if(isUser == 0 && isPwd == 0 && money < 500){
		//1,获取要调用方法类的字节码对象
		//jclass      (*FindClass)(JNIEnv*, const char*);
		jclass clazz = (*env)->FindClass(env, "com/lai/alipay/MainActivity");
		/**
		 *	2,获取要调用的方法
		 *	参数一:env
		 *	参数二:要调用的方法所在类的字节码对象
		 *	参数三:要调用的方法名字
		 *	参数四:方法的参数签名
		 *	jmethodID   (*GetMethodID)(JNIEnv*, jclass, const char*, const char*);
		 */
		jmethodID method = (*env)->GetMethodID(env, clazz, "showDialog", "(Ljava/lang/String;)V");

		/**
		 *	3,调用方法
		 *	参数一:env
		 *	参数二:要调用的方法
		 *	参数三:方法的参数,有参数就传没有就不用
		 *	void(*CallVoidMethod)(JNIEnv*, jobject, jmethodID, ...);
		 */
		(*env)->CallVoidMethod(env, obj, method, (*env)->NewStringUTF(env, "哈哈哈"));	//返回String类型需要这样的格式
	}
}
```

#### java调用C++

* 在java代码中创建一个本地方法
* 使用工具自动创建jni文件夹和里面的文件,不需要修改什么
* 在java代码中添加一个static代码块,并加载模块名
* 进入项目的src目录下,使用命令行生成.h文件,并复制到jni文件夹中
* 在.cpp文件中添加.h文件的名字



* java代码

```java

package com.example.helloc;

import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.widget.Toast;

public class MainActivity extends Activity {

	static{
		System.loadLibrary("helloc");
	}
	
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    } 
    
    private native String call();
    
    public void click(View view){
    	Toast.makeText(this, call(), 0).show();
    }
}

```

* .cpp代码

```java

#include <jni.h>
#include "com_example_helloc_MainActivity.h"

JNIEXPORT jstring JNICALL Java_com_example_helloc_MainActivity_call(JNIEnv* env, jobject obj){
	return (env)->NewStringUTF("mnmnmn");
}

```


  [1]: ./images/1.jpg "1"
  [2]: ./images/2.jpg "2"
  [3]: ./images/3.jpg "3"
  [4]: ./images/4.jpg "4"
  [5]: ./images/5.jpg "5"