# 反射

Java反射机制是在程序运行过程中,对于任何一个类,都能够知道它的所有属性和方法;对于任意一个对象,都能够知道调用它的任意属性和方法,这种动态获取信息以及动态调用对象方法的功能称为Java语言的反射机制

* 在运行时判断任意一个对象所属的类
* 在运行时构造任意一个类的对象
* 在运行时判断任意一个类所有的成员变量和方法
* 在运行时调用任意一个对象的方法

`Java.lang.reflect`包实现了反射机制

# Class类

在Java中,每定义一个java class实体都会产生以共Class对象

Class对象用于表示这个类的类型信息

Class中没有公共的构造器,不能被实例化

Class类中的方法:

```java
public String toString(){
    return( isInterface()? "interface":(isPrimitive():"":"class") )+getName();
}
/*
toString()方法能将对象转换为字符串,toString()首先会判断Class类型是否为接口,普通类和接口都能够用Class对象来表示,然后判断是否是基本数据类型,然后是getName()方法,这个方法返回类的全限定名称
*/

toGenericString();//这个方法会返回类的全限定名称,包括类的修饰符和类型参数信息
forName();//根据类名获得一个Class对象的引用,这个方法会使对象进行初始化
Class t=Class.forName("java.lang.Thread");//能够初始化一个线程对象
```

Field类

Field类提供类或接口中单独字段的信息,以及对单独字段的动态访问

常用方法

Method类

