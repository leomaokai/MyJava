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

