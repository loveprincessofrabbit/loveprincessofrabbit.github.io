# python笔记：条件判断与循环：
## 条件判断语句：
### ps：特别注意在写条件语句中的缩进式格式！！
#### 样例：age=20
####      if age>=19:(此处不要漏掉冒号)
####          print'your age is',age(打印的内容有多个时使用逗号分隔)(空四个格）
####      else:(注意冒号)
####          print'you are still a teenager'
#### 也可以使用elif（类似于c++中的else if）进行分支选择结构，同样不要忘了冒号以及格式
#### if语句的特点，if语句的判断顺序是从上到下，也就是说只要出现了一个符合要求的返回true的条件，那么剩下的判断将会被全部忽略；
#### if语句的简写： 如  if x：
####                       print 'ture' 只要x是非零数值，非空字符串，非空list等就会判断为true并且打印true 其他情况均为 flase；
## 循环语句：
### python的第一种循环方法：for xxx in xxx；
#### 其中的第一个xxx代表的是你要把什么东西赋值给一个叫做xxx的变量 而后面的xxx可以表示一个list
#### 实例：num={1,2，3}
####      for x in num:   该步指将num这个list中的内容依次赋值给x
####          print x     相当于c++中for循环中大括号的内容，没执行一次循环变打印一次x的当前值
#### ps： python中定义了一个range ，当range（5）便是{0,1,2,3,4}
### python的第二种循环方法：while循环
#### 在满足条件是进行while中的语句，不满足条件时退出循环
#### 实例：利用while循环计算0——100奇数的和
#### sum=0
#### n=99
#### while n>0 :
####     sum=sum+n
####     n=n-2
#### print sum
# Python笔记2 list和tuple：
## list：
### list类似于数组，定义一个list的方法为： list的名字={元素}，元素与元素之间用逗号隔开，一个list中可以使用不同数据类型的元素。
### 输入list的名字即可显示list中所有元素如：
### 输入 classmate={'2b',2}             想要输出某个元素则
### 再输入 classmate 命令，输出：        输入 classmates [0] 即第一个元素若输入classmates[-1]为
### {'2b'，2}                          倒数第一个，-2是倒数第二个以此类推
### 对list中内容的修改：
### 1.在list的末尾增添一个新的元素：
### classmates.append(要增加的新的元素)
### 2.将某一个元素插入到list中指定的位置：
### classmates.insert(此处填想要的位置,此处填要加入的元素)
### 3.删除list末尾的元素：
### classmates.pop()
### 4.删除特定位置的元素：
### classmates.pop(i) i是要删除的元素位置标号
### 5.对某个位置的元素进行替换：
### 类似于数组的赋值，classmates[i]=新元素 将指定位置的元素直接替换
### ps：在一个list中可以加入另一个list，list中的list中的元素使用方法类似于二维数组的调用方法a[i][]
## tuple：
### tuple与list的获取某个元素的方法是相同的，但是tuple一旦经过定义就不能再改变
### tuple的定义格式：classmates=（元素），如果要定义一个空的tuple在括号里什么都不填就可以
### ps：当tuple中只含有一个元素时，由于python会将其理解为赋值t=1，因此为了避免混淆，要在唯一的元素后面加一个逗号
### 结合前面可以在list中加入另一个list的特点，在tuple中也可以加入一个list，这样便可以通过改变list中的元素间接的改变tuple。

### ps：在使用文本编辑器写python的程序时，要注意回车自动换行前面的空格是制表符，要避免制表符与空格混用