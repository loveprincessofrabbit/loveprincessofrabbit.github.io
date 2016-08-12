---
layout: post
title: "Python实现的字符版Bad Apple!!"
category: Python
tags: Python
excerpt: 听说有屏幕的地方就会有Bad Apple!!
---
> 如果觉得这篇文章写的不错，来给我[打赏鼓励](https://github.com/miaochiahao/miaochiahao.github.io/blob/master/pictures/alipay.jpg)一下吧~我会写出更好的文章


### 思路
老规矩，先讲思路：  

一开始看各种老司机们做的也是一脸懵逼，去查了各种资料看了各种视频，最给跪的是那个用示波器的和用任务管理器的… 

梳理一下，字符画版的思路就是把视频转成一帧一帧的图片，然后把图片都变成字符画，这样播放的时候只要按照一定的速度（比如每秒25帧），就能用视觉暂留原理生成动画了。也就是说：

* 把视频截成每秒25帧的图片
* 把图片转成字符画按顺序存到文件中
* 在文件中以每秒25帧的速度读取出来

以上~

### 视频截取

这个用的是KMPlayer自带的一个功能：高级捕获，能够以所需来截取视频。
![KMPlayer](https://github.com/miaochiahao/miaochiahao.github.io/blob/master/pictures/bad_apple_KMPlayer.png)

经过测试最后决定：
1. 每秒截取25帧
2. 分辨率需要特别注意：每个字符可以看作是一个像素点，由于字符高远比宽要长，要把高度酌情缩减。最后确定了用80\*30

总共截了3000+张
![Files](https://github.com/miaochiahao/miaochiahao.github.io/blob/master/pictures/bad_apple_screenshots.png)

### 图像处理

图像处理部分简单地说就是；
1. 遍历每个像素点，把每个像素点按照ARGB值按照一定规则转化为灰度值
2. 建立一个字符集来供程序挑选字符来代替相应的灰度值
3. 将每个像素点根据规则转化为字符

{% highlight python %}
# -*- coding:utf-8 -*-
from PIL import Image
import argparse
import sys

ascii_char = r"$@B%8&WM#*oahkbdpqwmZO0QLCJUYXzcvunxrjft/\|()1{}[]?-_+~<>i!lI;:,\"^`'" # 定义所需的字符列表

def pixel_to_char(r,b,g,alpha=256):	# 传入ARGB值
	if alpha == 0:	# 若为透明像素，返回空格
		return " "
	length = len(ascii_char)
	gray = int (0.2126*r+0.7152*g+0.0722*b)		# 转化为灰度值
	
	unit = (256.0+1)/length		# 将ascii_char均匀分布到灰度值，也就是计算出每个字符占多少个单位的灰度值
#	print int(gray/unit)
	return ascii_char[int(gray/unit)]	# 根据灰度值来计算返回哪个字符

def get_pixel(file):

	img = Image.open(file)
	width = img.size[0]
	height = img.size[1]
#	print img.size
	result = []
	for h in range(0,height):
		for w in range(0,width):
			pixel = img.getpixel((w,h))
			result.append(pixel)
	print file
	return result

def stdout (result,textfile):
	count = 1
	for item in result:
	
	#	pass
		textfile.write(pixel_to_char(item[0],item[1],item[2]))
#		sys.stdout.write(pixel_to_char(item[0],item[1],item[2]))
		if count % 80 ==0:
	#		pass
#			sys.stdout.write('\n')
			textfile.write('\n')

		count +=1

textfile=open(r'F:\text.txt','w')

templist=[]
for item in range(49,3530):
	if item <100:
		templist.append(("00"+str(item)))
	elif item <1000:
		templist.append(("0"+str(item)))
	else:
		templist.append(str(item))


filename=[]
for num in templist:

		filename.append(r"C:\KMPlayer\Capture\bad_apple"+num+r".bmp")
#print filename

for file in filename:
	result=get_pixel(file)
	stdout(result,textfile)

raw_input()

{% endhighlight %}

就这样~转化好的字符画图像被写入了文件`text.txt`，总共8Mb多
![Text.txt](https://github.com/miaochiahao/miaochiahao.github.io/blob/master/pictures/bad_apple_Picture.png)

### 文件读取

最后一步就是把这些已经存好的数据按照一定速度读出来，本来想每次放30行显示在Shell里面，然后清屏，再换新的，但是Windows的CMD里运行的Python貌似没有提供清屏的功能。

于是思考了一下电影放映之类的原理，决定按照极快的速度去把每行都写进去，然后每30行停一下（类似在放映时手摇那个放映器）停的时间就能自己控制，以此控制放映速度。

{% highlight python %}
import sys
textfile=open(r'F:\text.txt','r')
i = 1
for line in textfile.readlines():

	sys.stdout.write(line)
#	print i
	if i % 30==0:
		c = 1
		while c <= 500000:
			c+=1
	i +=1
{% endhighlight %}

细心的观众老爷应该发现了，我在这里用的是`sys.stdout.write( )`而非`print`，这是因为在Python中每次调用`print`，如果是追加模式（不换行），就会自动多出一个空格；如果打印行，他就会再多出一行，这样把整个像素的间距拉大了非常不方便。而使用系统的标准输出就不会有这样的问题~

只要改变c的范围，就能去调整速度

所以有人打赏吗！！！快过生日了啊喂！！！

没人打赏来留个言也行啊！！！寂寞如雪啊！！！