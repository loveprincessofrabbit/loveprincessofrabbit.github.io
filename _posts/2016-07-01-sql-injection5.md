---
layout: post
title: "SQL注入（五）"
category: SQL注入
tags: SQL注入 渗透测试
---

## 突破magic_quotes_gpc与addslashes()

### 转化为十六进制

若SQL在`php.ini`配置文件中`magic_quotes_gpc`为`on`，或者在接受参数时使用了`addslashes()`函数，单引号就会被自动转义成`\'`,从而影响函数功能正确运行。

要绕开过滤，可以将所需命令转化为十六进制，再进行提交

### 宽字节注入

当`magic_quotes_gpc`为`on`时，所有的单引号、双引号、null字符都会被在前面自动加上一个反斜杠进行转义。其他类似作用的函数还有`addslashes()`、`mysql_escape_string`、`mysql_real_escape_string`等。

宽字节注入的原理是通过构造十六进制的占两个字节的汉字来代替反斜杠`\`，从而破坏转义。如果程序默认字集是GBK等宽字节字符集，则MySQL用GBK的编码时，会认为`%df
`是个宽字符，即`運`。也就是说`%df\'`等同于`%df%5c%27`等同于`運'`

一个汉字占两个字节，称宽字节；一个字母和一个数字均占一个字节，称单字节。汉字中的繁体字转换之后均为十六进制形式，如`%df`表示`運`

举例来看，当过滤开启时，我们输入单引号

`search.php?n=1'`

实际提交的是
{% highlight php %}
select * from user where username='1\''
{% endhighlight %}

而当我们输入了一个宽字节`%df`其中`%`等同于`0x`

`search.php?n=1%df'`

实际提交的则是
{% highlight php %}
select * from user where username='1運''
{% endhighlight %}

其中運字干掉了反斜杠，占用了两个字符，从而释放出了单引号

## 针对宽字节注入的防御

解决方案就是在初始化连接和字符集之后，使用 `SET character_set_client=binary` 来设定客户端的字符集是二进制的。
例如：
{% highlight sql %}
mysql_query("SET character_set_client=binary")
{% endhighlight %}

## 哪些地方没有魔术引号的保护

* $_SERVER变量
* getenv()得到的变量
* $HTTP_RAW_POST_DATA与PHP输入、输出流

（这些并不懂