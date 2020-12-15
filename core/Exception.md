# 异常

**Throwable**

* Error:严重问题,不需要处理
* Exception:称为异常类,表示程序本身可以处理的问题
  * RuntimeException:在编译期是不检查的,出错后,需要我们修改代码
  * 非RuntimeException:编译期就必须处理的,否则无法通过编译

```java
//返回抛出异常的详细信息
public String getMessage();
public String getLocalizedMessage();

//返回异常发生时的简要描述
public String toString();

//打印异常信息到标准输出流上
public void printStackTrace();
public void printStackTrace(PrintStream s);
public void printStackTrace(PrintWriter s);

//记录栈帧的当前状态
public synchronized Throwable fillInStackTrace();
```

**JVM默认处理方案**

* 把异常名称,异常原因及异常出现的位置等信息输出在了控制台
* 程序停止执行

**异常处理**

```java
try {
    可能出现异常的代码;
} catch(异常类名 变量名) {
    异常的处理代码;
}//可以使程序执行完毕

public static void method(){
    try{
        int [] arry={1,2,3};
        System.out.println(arry[3]);//数组访问越界异常
    } catch (ArrayIndexOutOfBoundsException e){
        //System.out.println("error");
        e.printStackTrace();
    }
}

//Throwable的成员方法
public String getMessage();//返回错误的详细消息字符串
public String toString();//返回此错误可抛出的简短描述
public void printStackTrace();//把错误信息输出在控制台
```

**编译时异常和运行时异常的区别**

* 所有RuntimeException类及其子类被称为运行时异常,其它异常都是编译时异常
* 编译时异常必须显示处理,否则无法通过编译(受检异常)
* 运行时异常无需显示处理,也可以和编译时异常一样处理(非受检异常)

**trows**

trows处理异常方案

```java
public static void method() throws ArrayIndexOutOfBoundsException{
    int [] arry={1,2,3};
    System.out.println(arry[3]);
}//并没有做实际的处理,真正的处理还得try,抛出去让调用者处理

public static void main(String[] args){
    try{
        method();
    } catch(ArrayIndexOutOfBoundsException e){
        e.printStackTrace();
    }
}
```

# 自定义异常

```java
public class ExceptionName extends Exception{
    public ExceptionName(){}
    public ExceptionName(String message){
        super(message);
    }
}

public class test{
	public void testfunction(int n) throws ExceptionName{
        if(n<0 || n>100){
            //throws new ExceptionName();
            throw new ExceptionName("n error");//throw抛出异常
        }else{
            System.out.println("ok");
        }
    }
}

public static void main(String [] args){
    test t=new test();
    try{
        t.testfunction(100);
    }catch(ExceptionName e){
        e.printStackTrace();
    }
}
```

throws和throw的区别:

* throws用在方法声明后面,跟着是异常类名,表示抛出异常,由该方法的调用者来处理,是抛出异常的一种可能性,并不一定会法生这些异常
* throw用在方法体内,后面是异常对象名,`throw new 异常类名();`表示抛出异常,由方法体内的语句处理,执行时一定抛出了某种异常