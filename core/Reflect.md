# 注解

## 内置注解

```
@Override:重写注解
@Deprecated:不推荐使用
@SupperssWarnings("all"):用来抑制编译器时的警告信息
```

## 元注解

```
@Target:用于描述注解的使用范围
@Retention:表示需要在什么级别保存该注释信息,用于描述注解的生命周期
@Documented:说明该注解将被包含在javadoc中
@Inherited:说明子类可以继承父类中的该注解
```

```java
//自定义一个注解,使用@interface
@Target(value={ElemenType.METHOD,ElemenType.TYPE})
@Retention(value=ElemenType.RUNTIME)
@Documented
@Inherited
@interface MyAnnotation{
}
```

## 自定义注解

```java
@Target(value={ElemenType.METHOD,ElemenType.TYPE})
@Retention(value=ElemenType.RUNTIME)
@interface MyAnnotation{
    //注解的参数:参数类型+参数名();
    String name() default "";//默认为空
    int age() default 0;
    int id() default -1;//如果默认值为-1,代表不存在
   	String[] schools() default {"cumt","beida"};
}

@Target(value={ElemenType.METHOD,ElemenType.TYPE})
@Retention(value=ElemenType.RUNTIME)
@interface MyAnnotation2{
    String value();//只有一个参数,使用value在赋值时可省略
}
```


# 反射

Java反射机制是在程序运行过程中,对于任何一个类,都能够知道它的所有属性和方法;对于任意一个对象,都能够知道调用它的任意属性和方法,这种动态获取信息以及动态调用对象方法的功能称为Java语言的反射机制

* 在运行时判断任意一个对象所属的类
* 在运行时构造任意一个类的对象
* 在运行时判断任意一个类所有的成员变量和方法
* 在运行时调用任意一个对象的方法

`Java.lang.reflect`包实现了反射机制

# Class类

在Java中,每定义一个java class实体都会产生以共Class对象

加载完类之后,在堆内存的方法区就产生了一个Class类型的对象(一个类只有一个Class对象),这个对象包含了完整的类的结构信息

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

## 得到Class

```java
//通过对象获得
Class c1=person.getClass();
//froname获得
Class c2=Class.forName("Clsspath");
//通过类名.class获得
Class c3=Student.class;

//获得父类类型
Class c5=c1.getSuperclass();
```



