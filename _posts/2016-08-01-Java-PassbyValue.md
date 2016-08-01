---
layout: post
title: "Java传参问题：按值传递还是按引用传递"
tags: Java
category: Java
excerpt: 整理了一下Java传参问题
---
> ~~如果觉得这篇文章写的不错，来给我[打赏鼓励](http://d3.freep.cn/3tb_160710193136wkl4568789.jpg)一下吧~我会写出更好的文章~~写的不好不要赏了0.0

## Java传参问题：按值传递还是按引用传递

最近写东西的时候总是避不开这个问题，经常拿不准给方法传过去一个值并修改之后，原有值是否发生了改变，于是查了一些东西简单整理一下。

### Java的基本类型
基本类型可以分为三类：
1. 字符型：char
2. 布尔类型：boolean
3. 数值类型：byte, short, int, long, float, double

为什么要先说这个呢0.0，因为经测试基本类型和引用类型在传递时是有差异的

### 一些简单测试
用以下代码进行了一个简单的测试：
{% highlight java %}
public class PassByValue {

	public void Changeint(int i) {
		i++;
		System.out.println("修改后方法内i的值为：" + i);
	
	}
	
	public void ChangeString(String s) {
		s = "World~~~";
		System.out.println("修改后方法内s的值为：" + s);
	
	}
	
	public void ChangeBuffer(StringBuffer sb) {
	
		sb = sb.append("SSS");
		System.out.println("修改后方法内sb的值" + sb);
	
	}
	
	public static void main(String[] args) {
		int i = 1;
		String s = "Hello";
		StringBuffer sb = new StringBuffer("AAA");
	
		System.out.println("修改前属性i的值为：" + i);
		PassByValue pb = new PassByValue();
		pb.Changeint(i);
		System.out.println("修改后属性i的值为：" + i);
	
		System.out.println("修改前属性s的值为：" + s);
		pb.ChangeString(s);
		System.out.println("修改后属性s的值为：" + s);
	
		System.out.println("修改前属性sb的值为：" + sb);
		pb.ChangeBuffer(sb);
		System.out.println("修改后属性sb的值为：" + sb);
	}

}

{% endhighlight %}

输出结果如下：
{% highlight java %}
修改前属性i的值为：1
修改后方法内i的值为：2
修改后属性i的值为：1
修改前属性s的值为Hello
修改后方法内s的值为：World~~~
修改后属性s的值为：Hello
修改前属性sb的值为：AAA
修改后方法内sb的值：AAASSS
修改后属性sb的值为：AAASSS

{% endhighlight %}

可见对于int类型，在传入方法进行修改后，原值i并没有改变。对于StringBuffer类型，原值在传入方法后，被修改了。

### 没什么卵用的分析

实际上，对于基本类型，在传参时传递是按值传递的，也就是说在传递时会把传递一份值的副本，任何修改都将是对这个副本的修改，对原值没有影响。而按引用传递则传递的是这个对象的引用，即指向他的内存地址。Java按照这个地址直接找到内存中的数据，并进行修改，这样不管是原值还是在方法中被修改的值，他们都发生了变化。

听说Java中官方的说法是都是按值传递的，并不存在按引用传递，因为在传递引用时，这个引用值（内存地址）是按值传递的。

以及这样就解释了为何int类型和StringBuffer类型的对象传入以后被修改的情况不一样。还有一点神坑的就是String类型的对象了，明明String不属于基本类型，但上面的测试结果显示对于属性s，修改前修改后并没有变化，只在函数运行中发生了改变。

这就和String类型本身有关了，String不是可变类型，他产生后值不会发生改变。我们对String值的修改，其实是在内存开辟了一块新的空间，存储了另一块数据，将新内存的地址指向了String对象。在传递String对象时，String对象的地址副本被传递进去，属性s在方法中被修改后，s指向了一个新的内存地址，然而由于没有对原地址的数据进行修改、这个新的地址生效的范围只在这个方法内，也就导致了s的值在方法外依旧没有变化。

大概搞清楚了一些，收工。
BTW，感觉写的乱七八糟的，还请各位老司机多指正。
