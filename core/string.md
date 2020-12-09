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

