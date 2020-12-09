```java
//数组的定义格式
int [] arry;	//定义了一个int类型的数组,数组名是arry
int arry[];		//定义了一个int类型的变量,变量名是arry数组

//数组动态初始化
int [] arry = new int[10];
//数组元素访问
System.out.println(arry);		//输出数组名
System.out.println(arry[0]);	//输出数组元素
//静态初始化
int [] arry=new int[]{1,2,3};
int [] arry={1,2,3};
//遍历
for(int x=0 ; x < arry.length ; x++){
    System.out.println(arry[x]);
}
//获取最值
int max=arry[0];
for(int x=0;x<arry.length;x++){
    if(arry[x]>max){
        max=arry[x];
    }
}
```

