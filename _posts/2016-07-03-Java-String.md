---
layout: post
title: "Java中连接字符串不同方式的比较"
category: Java
tags: Java
excerpt: 本文分析了在Java中连接字符串的不同方式耗时情况，以及对背后原因的分析，最终尝试探究连接字符串最高效的方式。
---

## 连接字符串的不同方式

在Java中连接字符串通常有以下几种方式：

* 用`+`来直接叠加连接
* 用`String`内置方法`concat()`来连接
* 使用`StringBuffer`内置方法`append()`来连接
* 使用`StringBuilder`内置方法`append`来连接

但不知道以上几种方法是否会有时间复杂度和空间复杂度上的区别，故计算了每种方式在相同任务下所需的时间来分析：

{% highlight java %}
// 叠加字符串
long start = System.currentTimeMillis();
String str = "abc";
for (int i = 0; i < j; i++) {
     str += "def";
 }
long end = System.currentTimeMillis();
long time = end - start;
System.out.println("字符串相加法耗时为"+time+"毫秒");
{% endhighlight %}

{% highlight java %}
//使用String内置函数concat()来连接
start = System.currentTimeMillis();
str = "abc";
for (int i = 0; i < j; i++) {
    str = str.concat("def");
}
end = System.currentTimeMillis();
time = end - start;
System.out.println("concat法耗时为"+time+"毫秒");
{% endhighlight %}

{% highlight java %}
//使用StringBuffer内置append()方法连接
start = System.currentTimeMillis();
StringBuffer stbr = new StringBuffer("abc");
for (int i = 0; i < j; i++) {
    stbr = stbr.append("def");
}
end = System.currentTimeMillis();
time = end - start;
System.out.println("StringBuffer内置append方法耗时为"+time+"毫秒");
{% endhighlight %}

{% highlight java %}
//使用StringBuilder内置append()方法连接
start = System.currentTimeMillis();
StringBuilder stbd = new StringBuilder("abc");
for (int i = 0; i < j; i++) {
    stbd = stbd.append("def");
}
end = System.currentTimeMillis();
time = end - start;
System.out.println("StringBuilder内置append方法耗时为"+time+"毫秒");
{% endhighlight %}

## 不同方式所耗时间的比较

|方法|j=1000|j=10000|j=100000|j=1000000|
|:----:|:------:|:----:|:----:|:-----:|
|字符串相加|5|198|10101|1290796|
|String.concat()|2|97|4278|553904
|StringBuffer.append()|0|3|6|20|
|StringBuilder.append()|0|1|4|15|

嗯，在等j=1000000的时候我起身倒了杯水…倒了杯水…倒了杯水…又倒了一杯

效果还是<del/>十分明显</del>令人发指的，看来不同的方法执行同一个任务在任务量巨大时的确有差异


## 初步猜想与分析

### 字符串相加

String类是不可变的，会在内存中造成大量冗余。`"1"+"2"+"3"+..."n"`会产生`[n+(n+1)]/2`个String对象。故String对象相加时，会生成`abc`、`def`、`abcdef`等若干对象。虽然有String常量池机制来保证String常量的可利用性，但依旧每次执行相加命令时会创建好一个新的字符串。

### String内置函数concat()

`concat()`函数的实质是计算新的字符串的长度，根据这个长度新建了一个字符型数组，使其长度为原有字符串长度与新增字符串长度之和，又将原来的文字后面加入了新的字符型数组，其本质还是数组的合并操作。

关于`copyOf()`函数

>Copies the specified array, truncating or padding with null characters 
>(if necessary) so the copy has the specified length. For all indices that are 
>valid in both the original array and the copy, the two arrays will contain 
>identical values. For any indices that are valid in the copy but not the 
>original, the copy will contain '\\u000'. Such indices will exist if and only
>if the specified length is greater than that of the original array.

### StringBuffer内置函数append()

StringBuffer是一个可变的类，其大小和内容均可以随时更改，不会存在String类一样的内存冗余的问题。同样是在StringBuffer后面加入字符型数组。


### StringBuilder内置函数append()

StringBuilder同StringBuffer，但是是非线程安全的。关键在于第三种和第四种方法的比较

## Java栈操作相关指令

|操作码|操作数|说明|
|---|----|----|
|ldc|无符号8位数indexbyte|从由indexbyte指向的常量池入口中取出一个字长的值，然后将其压入栈|
|astore_1|无|从栈中弹出对象引用，然后将其存到位置为1的局部变量中|
|iconst_0|无|将int类型值0压入栈|
|istore|vindex|从栈中弹出int类型值，然后将其存到位置为vindex的局部变量中|
|istore_2|无|从栈中弹出int类型值，然后将其存到位置为2的局部变量中|
|iload_2|无|将位置为2的int类型的局部变量压入栈|
|sipush|一个short类型的数|将short类型的数转换为int类型的数，然后压入栈|
|new|index|在堆中创建一个新的对象，将其引用压入栈|
|dup|无|复制栈顶部的一个字|
|invokespecial|无|调用实例方法（静态绑定，速度快）|
|aload_1|无|将位置为1的对象引用局部变量压入栈|
|invokevirtual|无|调用实例方法（动态绑定）|
|iinc|vindex,const|把常量与一个位于vindex位置的int类型局部变量相加|
