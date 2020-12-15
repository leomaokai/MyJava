[Java帮助文档](https://docs.oracle.com/javase/8/docs/api/)

# String

```java
String s1=new String();
char [] chs={'a','b','c'};
String s2=new String(chs);//abc
byte [] bys={97,98,99};
String s3=new String(bys);//abc
String s4="abc";//abc,没有使用构造方法
//推荐使用直接赋值的方式,new方法每次new一个新的对象
String s5="abc";//s5和s4本质上是同一个对象

//字符串比较
s1==s2;//比较字符串地址是否相同
s1.equals(s2);//比较字符串内容是否相同
s4==s5;//true 直接赋值方式为同一个地址

//字符串遍历
//length()返回字符串的长度
//charAt(i)返回i位置字符
String username="leomaokai";
for (int i = 0; i < username.length(); i++) {
    System.out.println(username.charAt(i));
}

//字符串拼接
String s1="hello";
s1+="world"; //hello world,每次拼接会构建新的String对象

//StringBuilder是一个可变的字符串类,内容可变,而String不可变
StringBuilder sb=new StringBuilder();
sb.append("hello");//append()方法返回对象本身
sb.append("world");//helloworld
sb.append("hello").append("Java");
sb.reverse();//reverse()方法将sb内容进行反转

//StringBuilder和String相互转换
StringBuilder sb = new StringBuilder();
sb.append("hello");
String s = sb.toString();//Builder-->String
StringBuilder sb2 = new StringBuilder(s);//String-->Builder
```

# ArrayList

```java
//ArrayList 
//软件包java.util
//可调整大小的数组实现,类似于C++的vector

//插入索引最大为最后一个元素的索引加1
ArrayList<String> array=new ArrayList<String>();
array.add("hello");//[hello]
array.add("world");//[hello,world]
array.add(1,"java");//[hello,java,world]

//常用方法,注意索引不能越界
array.get(0);//返回指定索引处的元素
array.size();//返回集合元素的个数
//删除
array.remove("hello");//删除hello成功,返回true
array.remove(0);//删除0索引元素,返回该元素
//修改
array.set(1,"c++");//将1索引元素改为c++,返回被修改的元素

//遍历集合
for(int i=0;i<array.size();i++){
    System.out.println(array.get(i));
}
for(string s:array){
    System.out.println(s);
}
```

# Math

# System

# Object

