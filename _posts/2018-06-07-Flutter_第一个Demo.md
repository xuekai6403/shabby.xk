---
layout:     post
title:     Flutter_第一个Demo
subtitle:   
date:       2018-06-07
author:     XueKai
header-img: 
catalog: true
tags:
---  

>main.dart

```
import 'package:flutter/material.dart';
import 'package:english_words/english_words.dart';
import 'XKListView.dart';
void main() => runApp(new myApp());

class myApp extends StatelessWidget{
  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return new MaterialApp(
      title: "title",
      theme: new ThemeData(
        primaryColor: Colors.white,
      ),
      home: new Center(
        child: new RandomWords(),
      ),
    );
  }
}
class RandomWords extends StatefulWidget{
  @override
  createState() => new RandomWordsState();
}
class RandomWordsState extends State<RandomWords>{
  final _suggestions = <WordPair>[];
  final _biggerFont = const TextStyle(fontSize: 15.0);
  final _saved = new Set<WordPair>();
  @override
  Widget build(BuildContext context){
    return new Scaffold(
      appBar: new AppBar(
        title: new Text("FlutterXK"),
        actions: <Widget>[
          new IconButton(icon: new Icon(Icons.list), onPressed: null),
        ],
      ),
      body: _buildListView(),
    );
  }
  ListView _buildListView(){
    return new ListView(
      children: <Widget>[
        new ListTile(
          title: new Text("ListView"),
          onTap: (){
            Navigator.of(context).push(
                new MaterialPageRoute(
                    builder: (context){
                      return new ListViewVC();
                    }
                )
            );
          },
        ),
        new ListTile(
          title: new Text("GridView"),
        ),
      ],
    );
  }
}
```

![Image]({{ site.url }}/img/flutter011.jpg)

<hr>

<br>

>XKListView.dart

```
import 'package:flutter/material.dart';
class ListViewVC extends StatelessWidget{
  @override
  Widget build(BuildContext context){
    return new Scaffold(
      appBar: new AppBar(title: new Text("ListView"),),
      body: new ListView(
        children: <Widget>[
          new ListTile(title: new Text("张三"),subtitle: new Text("中国人"),),
          new ListTile(title: new Text("李四"),subtitle: new Text("朝鲜人"),),
          new ListTile(title: new Text("王五"),subtitle: new Text("美国人"),),
          new ListTile(title: new Text("詹姆斯"),subtitle: new Text("中国人"),),
          new ListTile(title: new Text("JR史密斯"),subtitle: new Text("古巴人"),),
          new ListTile(title: new Text("史蒂芬库里"),subtitle: new Text("韩国人"),),
          new ListTile(title: new Text("凯文杜兰特"),subtitle: new Text("西班牙人"),),
          new ListTile(title: new Text("张三"),subtitle: new Text("中国人"),),
          new ListTile(title: new Text("李四"),subtitle: new Text("朝鲜人"),),
          new ListTile(title: new Text("王五"),subtitle: new Text("美国人"),),
          new ListTile(title: new Text("蒂姆邓肯"),subtitle: new Text("中国人"),),
          new ListTile(title: new Text("迈克尔乔丹"),subtitle: new Text("古巴人"),),
          new ListTile(title: new Text("科比布莱恩特"),subtitle: new Text("韩国人"),),
          new ListTile(title: new Text("ShaQ"),subtitle: new Text("西班牙人"),),
        ],
      ),
    );
  }
}
```
![Image]({{ site.url }}/img/flutter012.png)
