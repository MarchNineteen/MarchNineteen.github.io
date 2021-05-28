---
title: java标识符与关键字
date: 2018-05-16 
desc:
keywords: java keyWord final
categories: [JavaSE]
---
# 标识符
- 概念：就是用于给程序中的变量、类、方法命名的符号;
     
- 标识符规则：标识符可以有字母、数字、下划线_、和美元符号$组成，并且数字不能打头
     标识符不能使java关键字和保留字，但可以包含关键字和保留字
     标识符不能包含空格 标识符只能包含美元符号$，不能包含@、#等其他特殊字符
- 分隔符：分号; 花括号{} 方括号[] 括号() 空格 圆点.;

# Java关键字
java中包含50个关键，所有关键字都是小写的
    
    关键字列表：
     abstract抽象的            assert 
     boolean                   break 
     byte                      case
     catch                     char
     const(保留字)             continue
    default                    do
     double                    else
     enum                      extends
     final                     finally
     float                     for
     if                        goto(保留字)
     implements                import
     int                       interface
     long                      native
     new                       package
     private                   protected
     public                    return 
     short                     static 
     strictfp                  super
     switch                    synchronized
     this                      throw
     throws                    transient
     try                       void 
     volatile                  while
 
     三个特殊的直接量（iteral）;true false null 都不是关键字
     
   
     final、finally、finalize的区别:
     final:
     final修饰变量，则等同于常量,变量不能修改
     final修饰方法中的参数，称为最终参数，参数不能修改。
     final修饰类，则类不能被继承
     final修饰方法，则方法不能被重写。
     finally:
     try-catch模块中使用finally，表示finally块则是无论异常是否发生，都会执行finally块的内容
     finalize:
     使用finalize在垃圾收集器确定这个对象未被引用时调用，表示在垃圾收集器将对象从内存中清除出去之前做一些清理工作。
     子类可以覆盖该方法以实现资源清理工作，GC在回收对象之前调用该方法