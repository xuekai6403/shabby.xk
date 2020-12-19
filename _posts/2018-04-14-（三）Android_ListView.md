---
layout:     post
title:      （三）Android_ListView
subtitle:
date:       2018-04-14
author:     XueKai
header-img:
catalog: true
tags:
---


![]({{ site.url }}/img/android_apps.jpg)



>ListView是安卓最经典的控件之一，相当于IOS的UITableView，使用率非常之高。虽然现在其江山地位不稳，将要被recyclerview取代，但设计理念是很经典的。

**ListView主要三个要素**

- ListView控件
- 适配器类，用到了设计模式中的适配器模式，它是视图和数据之间的桥梁，负责提供对数据的访问，生成每一个列表项对于的View。常用的适配器类有：ArrayAdapter，SimpleAdapter和SimpleCursorAdapter。
- 数据源，当然最重要最复杂的部分就是适配器类的编写和设计，在一些复杂的界面，常常需要对适配器类进行相关逻辑处理。


### 1.ArrayAdapter适配器

布局文件：

```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <ListView
        android:id="@+id/lv"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:divider="@android:color/holo_red_dark"
        android:dividerHeight="3dp"
        android:scrollbars="none"/>
</RelativeLayout>
```

```
public class MainActivity extends Activity {
    private ListView listView;
    private String datas[]={ "Sunday", "Monday", "Tuesday", "Wednesday",
            "Thursday", "Friday", "Saturday" };//准备数据源
    ArrayAdapter<String> adapter;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        requestWindowFeature(Window.FEATURE_NO_TITLE);//隐藏标题栏
        setContentView(R.layout.activity_main);
        listView=(ListView)findViewById(R.id.lv);
        //实例化ArrayAdapter
        adapter=new ArrayAdapter<String>(this,android.R.layout.simple_list_item_1,datas);
        listView.setAdapter(adapter);//设置适配器
    }
}
这里实例化ArrayAdapter对象传入了三个参数，第一个参数是上下文对象，第二个参数是子项布局，这里调用了系统内容的布局，第三个参数是数据集，这里传入的是字符串数组。最后调用setAdapter方法设置适配器。 
```

### 2.SimpleAdapter

ArrayAdapter仅仅适用于显示信息比较单一的场景，若显示信息包含多种形式的数据，就不太适用了，就要考虑用SimpleAdapter,当存在多种数据结构的时候首先要考虑布局问题。因此首先要设置子项目布局文件。

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:orientation="horizontal"
    android:layout_height="match_parent">
    <ImageView
        android:id="@+id/img"
        android:src="@mipmap/ic_launcher"
        android:layout_width="50dp"
        android:layout_height="50dp" />
    <TextView
        android:id="@+id/tv"
        android:text="hello"
        android:gravity="center"
        android:layout_marginLeft="50dp"
        android:textSize="28sp"
        android:layout_width="wrap_content"
        android:layout_height="50dp" />
</LinearLayout>
```

以上样式包括两种控件，ImgeView和TextView，ImageView负责图片的显示，TextView对图片信息进行说明，采用线性布局的水平布局模式。 主布局文件如下：

```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <ListView
        android:id="@+id/listview"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</RelativeLayout>
```

主布局文件中仅配置了一个ListView控件，设置其宽高都是占据整个布局（match_parent）。 MainActivity.java:

```
public class MainActivity extends Activity {
    private ListView listView;
    private SimpleAdapter simpleAdapter;
    private List<Map<String, Object>> datas=new ArrayList<Map<String, Object>>();
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        listView=(ListView)findViewById(R.id.listview);
        initDatas();//初始化数据集
//实例化SimpleAdapter
        simpleAdapter=new SimpleAdapter(this,datas,R.layout.animal_layout,new String[]{"img","name"},new int[]{R.id.img,R.id.tv});        listView.setAdapter(simpleAdapter);//设置配置器
    }
    private void initDatas() {
        Map map1=new HashMap();
        map1.put("img",R.drawable.fish);
        map1.put("name","小金鱼");
        Map map2=new HashMap();
        map2.put("img",R.drawable.horse);
        map2.put("name","千里马");
        Map map3=new HashMap();
        map3.put("img",R.drawable.mouse);
        map3.put("name","米老鼠");
        datas.add(map1);
        datas.add(map2);
        datas.add(map3);
    }
}
```

SimpleAdapter的构造函数如下： SimpleAdapter(Context context, List<? extends Map<String, ?>> data, int resource, String[] from, int[] to) 实例化SimpleAdapter时要传入几个参数：
- context：即上下文对象，这里传入this；
- data:是一个包裹Map集合的List数据集，这里传入datas，程序初始化的数据集；
- resource：子项布局文件，这里是我们自定义的animal_layout.xml文件，传入R.layout.animal_layout；
- from:是一个字符串数组，字符串指的是Map中的键值，这里有两个键，即“img”和“name”；
- to:是一个int型数组，表示子项布局中对应控件的id，这里传入ImageView的id-R.id.img和TextView的id-R.id.tv即可。


### 3.BaseAdapter适配器

从上面的例子可以看出，ArrayAdapter一般适用于数据源数据种类比较单一的情形，若数据类型比较复杂，需要个性化定制布局，则可以采用BaseAdapter适配器进行数据适配。若要问BaseAdapter和SimpleAdapter有什么不同，通过API文档可以获悉，SimpleAdapter是BaseAdapter的子类，BaseAdapter较SimpleAdapter来讲更为灵活，在开发中也更为常用。 下面通过一个实例，来说明在开发中是如何使用BaseAdapter适配数据源的。 

首先对复杂的数据源进行数据封装：

```
public class Animal {
    public Animal(String animal, int imgId) {
        this.animal = animal;
        this.imgId = imgId;
    }
    private String animal;
    private int imgId;
    public String getAnimal() {
        return animal;
    }
    public void setAnimal(String animal) {
        this.animal = animal;
    }
    public int getImgId() {
        return imgId;
    }
    public void setImgId(int imgId) {
        this.imgId = imgId;
    }
}
```

然后定义单个子项的布局：

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:orientation="horizontal"
    android:layout_height="match_parent">
    <ImageView
        android:id="@+id/img"
        android:src="@mipmap/ic_launcher"
        android:layout_width="50dp"
        android:layout_height="50dp" />
    <TextView
        android:id="@+id/tv"
        android:text="hello"
        android:gravity="center"
        android:layout_marginLeft="50dp"
        android:textSize="28sp"
        android:layout_width="wrap_content"
        android:layout_height="50dp" />
</LinearLayout>
```

主布局文件和ArrayAdapter的主布局文件一样，就不再贴出代码，下面看一下自定义的适配器类，继承自BaseAdapter，如下：

```
public class AnimalAdapter extends BaseAdapter {
    private Context context;
    private List<Animal> datas;
    //构造函数需要传入两个必要的参数：上下文对象和数据源
    public AnimalAdapter(Context context,List<Animal> datas) {
        this.context=context;
        this.datas=datas;
    }
    //返回子项的个数
    @Override
    public int getCount() {
        return datas.size();
    }
    //返回子项对应的对象
    @Override
    public Object getItem(int position) {
        return datas.get(position);
    }
    //返回子项的下标
    @Override
    public long getItemId(int position) {
        return position;
    }
    //返回子项视图
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        Animal animal= (Animal) getItem(position);
        View view;
        ViewHolder viewHolder;
        if(convertView==null){
            view = LayoutInflater.from(context).inflate(R.layout.animal_layout,null);
            viewHolder=new ViewHolder();
            viewHolder.animalImage=(ImageView)view.findViewById(R.id.img);
            viewHolder.animalName=(TextView)view.findViewById(R.id.tv);
            view.setTag(viewHolder);
        }else{
            view=convertView;
            viewHolder= (ViewHolder) view.getTag();
        }
        viewHolder.animalName.setText(animal.getAnimal());
        viewHolder.animalImage.setImageResource(animal.getImgId());
        return view;
    }
    //创建ViewHolder类
    class ViewHolder{
        ImageView animalImage;
        TextView animalName;
    }
}
```

**继承自BaseAdapter类，必须需要覆写四个方法，每个方法的具体含义已经在代码中做了注释。**此外，为了提高加载效率，这里创建了内部类ViewHolder，可以避免每次调用getView方法时都要通过findViewById方法去实例化控件，大大提高运行效率，推荐以后开发中这么使用。 MainActivity.java代码如下：

```
public class MainActivity extends Activity {
    private ListView listView;
    private List<Animal> datas = new ArrayList<Animal>();
    private AnimalAdapter animalAdapter;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        requestWindowFeature(Window.FEATURE_NO_TITLE);//隐藏标题栏
        setContentView(R.layout.activity_main);
        initDatas();
        listView = (ListView) findViewById(R.id.lv);
        animalAdapter = new AnimalAdapter(this, datas);
        listView.setAdapter(animalAdapter);
        listView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
            @Override
            public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
                Toast.makeText(MainActivity.this, "您单击了" + datas.get(position).getAnimal(), Toast.LENGTH_SHORT).show();
            }
        });
    }
    private void initDatas() {
        Animal animal0 = new Animal("兔八哥", R.drawable.rabbit);
        Animal animal1 = new Animal("眼镜蛇", R.drawable.snack);
        Animal animal2 = new Animal("小金鱼", R.drawable.fish);
        Animal animal3 = new Animal("千里马", R.drawable.horse);
        Animal animal4 = new Animal("米老鼠", R.drawable.mouse);
        Animal animal5 = new Animal("大国宝", R.drawable.panda);
        datas.add(animal0);
        datas.add(animal1);
        datas.add(animal2);
        datas.add(animal3);
        datas.add(animal4);
        datas.add(animal5);
    }
}
```

这里调用了setOnItemClickListener方法实现了单项监听，覆写了onItemClick方法，由参数positon通过List的getPosition方法获取对象，再通过对象封装的getAnimal方法可以获得对应的动物名，由Toast通知输出。


[极客学院](http://wiki.jikexueyuan.com/project/twenty-four-Scriptures/list-view.html)

