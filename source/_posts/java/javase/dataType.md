---
title: java数据类型
date: 2018-05-17 
desc:
keywords: 数据类型 字符编码 
categories: [JavaSE]
---
# 1.Java 八大基本数据类型及其封装器类（位数为二进制位）
## 字符类型 
char：默认值：\u0000，16位，数据范围：\u0000 - u\ffff，存储Unicode码，用单引号赋值。封装类：Character
## 布尔类型
boolean：默认值：false，1位，只有true和false两个取值。封装类：Boolean
## 数值类型
整数类型：(最大存储容量为2的n次方减一，范围为负数2的n减一次方至2的n减一次方减一，n为位数，原理见计算机组成原理无符号数与有符号数)

byte：默认值：0，8位，字节，最大存储数据量是255，存放的数据范围是-128~127之间。封装类：Byte

short：默认值：0，16位，最大数据存储量是65536，数据范围是-32768~32767之间。封装类：Short

int：默认值：0，32位，最大数据存储容量是2的32次方减1，数据范围是负的2的31次方到正的2的31次方减1。封装类：Integer

long：默认值：0，64位，最大数据存储容量是2的64次方减1，数据范围为负的2的63次方到正的2的63次方减1。封装类：Long

浮点数类型：

float：默认值：0.0f，32位，数据范围在3.4e-45~1.4e38，直接赋值时必须在数字后加上f或F。封装类：Float

double：默认值：0.0d，64位，数据范围在4.9e-324~1.8e308，赋值时可以加d或D也可以不加。封装类：Double

对于数值类型的基本类型的取值范围，我们无需强制去记忆，因为它们的值都已经以常量的形式定义在对应的包装类中了。如：

基本类型byte 二进制位数：Byte.SIZE最小值：Byte.MIN_VALUE最大值：Byte.MAX_VALUE

基本类型short二进制位数：Short.SIZE最小值：Short.MIN_VALUE最大值：Short.MAX_VALUE

基本类型char二进制位数：Character.SIZE最小值：Character.MIN_VALUE最大值：Character.MAX_VALUE

基本类型double 二进制位数：Double.SIZE最小值：Double.MIN_VALUE最大值：Double.MAX_VALUE

> 注意：float、double两种类型的最小值与Float.MIN_VALUE、 Double.MIN_VALUE的值并不相同，实际上Float.MIN_VALUE和Double.MIN_VALUE分别指的是 float和double类型所能表示的最小正数。也就是说存在这样一种情况，0到±Float.MIN_VALUE之间的值float类型无法表示，0 到±Double.MIN_VALUE之间的值double类型无法表示。这并没有什么好奇怪的，因为这些范围内的数值超出了它们的精度范围。

# 2.数据类型之间的转换：
 
## 简单类型数据间的转换 
自动转换：运算或者方法调用时，低精度自动转化为高精度，从低到高顺序：(byte，short，char)--int--long--float—double；
        特例：int到float，long到float，long到double 是不会自动转换的，不然将会丢失精度。
 
强制转换：高精度转为低精度，可以使用强制转换；即你必须采用下面这种语句格式： int n=(int)3.14159/2;可以想象，这种转换肯定可能会导致溢出或精度的下降。

## 表达式的数据类型自动提升
注意一下规则：
  
    ①所有的byte,short,char型的值将被提升为int型；
    ②如果有一个操作数是long型，计算结果是long型；
    ③如果有一个操作数是float型，计算结果是float型；
    ④如果有一个操作数是double型，计算结果是double型；
    例， byte b; b=3; b=(byte)(b*3);//必须声明byte。 

## 包装类过渡类型转换
- 简单类型的变量转换为相应的包装类，可以利用包装类的构造函数。即：Boolean(boolean value)、Character(char value)、Integer(int value)、Long(long value)、Float(float value)、Double(double value)
- 在各个包装类中，总有形为××Value()的方法，来得到其对应的简单类型数据。利用这种方法，也可以实现不同数值型变量间的转换，例如，对于一个双精度实型类，intValue()可以得到其对应的整型变量，而doubleValue()可以得到其对应的双精度实型变量

## 字符串与其它类型间的转换
其它类型向字符串的转换：

    ①调用类的串转换方法:X.toString();
    ②自动转换:X+"";
    ③使用String的方法:String.valueOf(X);

字符串作为值,向其它类型的转换：
    
    ①先转换成相应的封装器实例,再调用对应的方法转换成其它类型。例：new Float("11").doubleValue()
    ②静态parseXXX方法。
        String s = "1";
        byte b = Byte.parseByte( s );
        short t = Short.parseShort( s );
        int i = Integer.parseInt( s );
        long l = Long.parseLong( s );
        Float f = Float.parseFloat( s );
        Double d = Double.parseDouble( s );
    ③Character的getNumericValue(char ch)方法
    
## **自动拆箱与自动装箱**

自动装箱：把基本类型用它们对应的包装类包装起来，使它们具有对象的特质，可以调用所对应的包装类所定义的方法，比如toString()等。

```
Integer i0 = new Integer(0);//基本的创建封装类对象
Integer i1 = 2;//自动装箱
Integer i1_ = Integer.valueOf(2);//自动装箱的本质，调用XX.valueof()方法
```    

自动拆箱：跟自动装箱的方向相反，将Integer及Double这样的包装类的对象重新简化为基本类型的数据(自动拆箱时，一定要确保包装类的引用不为空)

```
System.out.println(i1+2);
//i1是我们上面通过自动装箱得到的一个integer对象，而这个对象是不能直接进行四则运算的，但是我们却给它+2，
这样就必须将integer对象转变为基本数据类型（int），这个过程就是自动拆箱的过程
```

jdk装箱池（方法区中的常量池）：java的八种基本类型（Byte Short、Integer、Long、Character、Boolean、Float、Double），除Float及Double意外，其它六种都实现了常量池，但是他们只在大于等于-128且小于等于127时才能使用常量池，如果不在此范围内，则会new一个出来，保存在堆内存中。

例子：

```
        Integer a = 1;
        Integer b = 1;
        Integer c = 144;
        Integer d = 144;
        Integer a1 = new Integer(1);
        Integer b1 = new Integer(1);
        System.out.println(a == b);         //true
        System.out.println(a.equals(b));    //true
        System.out.println(a1 == b1);       //false
        System.out.println(a1.equals(b1));  //true
        System.out.println(c == d);         //false
        System.out.println(c.equals(d));    //true
```

在进行==比较的时候，在自动装箱池范围内的数据的引用是相同的，范围外的是不同的。 

# 3.Java引用类型
Java有 5种引用类型（对象类型）：类 接口 数组 枚举 标注
引申：Java中的堆内存、栈内存、静态存储区
> http://www.cnblogs.com/mingziday/p/4899212.html

栈（stack）存放基础数据类型以及对象的对象的引用

堆（heap）存放由new创建的对象和数组，即堆主要是用来存储对象的

方法区中的静态存储区存储static声明的静态变量

# 4.java字符编码
> http://kxjhlele.iteye.com/blog/333211

