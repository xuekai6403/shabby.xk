---
layout:     post
title:      Flutter_webview
subtitle:   
date:       2018-06-19
author:     XueKai
header-img: 
catalog: true
tags:
---

<img src="/assets/postimg20/IMG_4869.jpg"/>

要想用Flutter做项目，Webveiw还是绕不过去的一个东西，现在大大小小的App众多详情和复杂布局，弱机器交互页面都会使用H5实现，今天就来简单总结一下Flutter中webvie的正确打开方式.

[官方Document](https://pub.dartlang.org/packages/flutter_web_view)

#### 1.导入

在你的pubspec.yaml文件中导入：

```
dependencies:
  flutter_web_view: "^0.0.2"
```

#### 2.引用并使用

```
import 'package:flutter_web_view/flutter_web_view.dart';
```

#### 3.Code

```
FlutterWebView flutterWebView = new FlutterWebView();

void launchWebViewExample() {
    flutterWebView.launch("https://juejin.im/entry/57ce6d5767f3560057b3002c",
        javaScriptEnabled: true,
        inlineMediaEnabled: true,
        barColor: Colors.white,
        tintColor: Colors.white);
    flutterWebView.listenForRedirect("http://127.0.0.1:8102/ws", true);
    flutterWebView.onWebViewDidStartLoading.listen((url) {
      print("111");
    });
    flutterWebView.onWebViewDidLoad.listen((url) {
      print("222");
    });
    flutterWebView.onRedirect.listen((url) {
      print("333");
    });
  }
```
