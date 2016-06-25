---
layout: post
tags: Python
title: "基于有道词典的python命令行词典程序"
category: Python
---

## 思路

* 抓取有道词典网页版html源代码
* 用BeautifulSoup库去处理和分析
* 找到相应tag（由于偏好柯林斯双解，抓取了id=‘collinsResult’的数据）

## 遇到的问题

* `The <b> world </b> is your own oyster` 把句子断开
* 一些句子里带有“→”符号
* unicode编码与其他编码不匹配的情况

## 解决方案

* 用replace()函数对文本进行了处理，去掉了tag
* 输出有用信息，讲信息分成了四类：
	1. 整句（包括英文例句、中文翻译、中文例句）
	2. 整句加其他成分（英文解释后面有汉语）
	3. 带有“→”符号的句子
	4. 词性，音标（词性全是大写字母，音标由//来标记）
* 对于第一、二类用了正则表达式，能够匹配的就打印出来；第三类用搜索；第四类直接判断大小写或者用正则。
* 然后要说编码的问题，由于中文标点不能用正则表达式去查找，只能手动搜索，就是查找最后一个字符是否匹配标点。很笨的用了无数判别式去分析
* 过程中出现了unicode和其他编码不匹配的情况，查找后发现中文标点这个字符串这个对象的类型为str而不是unicode，于是进行转换

## 代码如下

{% highlight python %}
 # -*- coding:utf-8 -*-     
import urllib2  
from bs4 import BeautifulSoup  
import re  
while True:  
    word=raw_input("Please input the word: ")  
    if word=="exit now":  
        exit()  
    print "\n"  
    url='http://dict.youdao.com/search?q=' + word + '&keyfrom=dict.index'  
    response=urllib2.urlopen(url)  
    html=response.read()  
    soup=BeautifulSoup(html)  
    right=("→")  
    right=right.decode('utf-8')  
    #To find all "collinsOrder" and "examples"  
    collins=str(soup.find_all(id='collinsResult'))  
    collins=collins.replace('<b>',' ')  
    collins=collins.replace('<//b>',' ')  
    collins=BeautifulSoup(collins)  
    for string in collins.stripped_strings:  
        pattern=re.compile('[\.\?!":]|.∗|\/')  
        match=pattern.search(string)  
        if string==('[').decode('utf-8'):  
            continue  
        if string==(']').decode('utf-8'):  
            continue  
        if match:  
            print string  
    #       print type(string)   #To see what type is the item "string" and to fix the bug !  
        elif string[-1]==("。").decode('utf-8'):  
            print string  
        elif string[-1]==("”").decode('utf-8'):  
            print string  
        elif string[-1]==("！").decode('utf-8'):  
            print string  
        elif string[-1]==("？").decode('utf-8'):  
            print string  
        elif string[-1]==("》").decode('utf-8'):  
            print string  
        elif string[-1]==("…").decode('utf-8'):  
            print string  
        elif string[-1]==("：").decode('utf-8'):  
            print string  
        elif string.find(right):  
            string=re.sub('\s+',' ',string)  
            print string          
        elif  string.isupper()==True:  
            print string  
        elif string==('[').decode('utf-8'):  
            print "break!"  
        elif string==("]").decode('utf-8'):  
            print "break!"  
    print "\n"  
{% endhighlight %}