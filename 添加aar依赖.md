---
title: 添加aar依赖
---
[TOC]

## 添加aar依赖

* 把aar包放到libs包中

![enter description here][1]

* 在该应用的build.gradle文件添加以下代码

![enter description here][2]

```xml
repositories {
    flatDir {
        dirs 'libs'
    }
}
```

* 添加依赖

![enter description here][3]

```xml
 compile(name:'aar的名字去除后缀', ext:'aar')
```


  [1]: ./images/aar1.jpg "aar1"
  [2]: ./images/aar2.jpg "aar2"
  [3]: ./images/aar3.jpg "aar3"