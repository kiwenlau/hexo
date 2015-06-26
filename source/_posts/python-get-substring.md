title: Python截取字符串的子串
date: 2015-06-21 16:31:40
tags: Python
---

Python处理字符串非常方便。这篇博客将通过一个简单的示例程序介绍如何使用Python截取字符串的子串。

![](/image/150621/python-substring.png)

**示例程序(example.py):**
```python
# coding=utf-8

str = "2015-06-21"

year = str[0:4]
month = str[5:7]
day= str[8:10]

print "日期: " + str
print "年: " + year
print "月: " + month
print "日: " + day
```
**执行程序：**
```sh
python example.py
```
**程序输出:**
```
日期: 2015-06-21
年: 2015
月: 06
日: 21
```
**分析：**
示例程序非常简单，它会截取日期字符串中的年月日。使用子串开始字符与结束字符的偏移量就可以很方便地进行截取。**但是，有两点需要注意**

- 字符串的偏移从0开始。例如，截取"2015"时使用str[0:4]而不是str[1:5]。
- 截取子串时，结束字符是子串最后一个字符的后面一个字符。例如，截取"2015"时使用str[0:4]而不是str[0:3]，而“5”的实际偏移量是3。这一点比较容易出现错误。

***
**版权声明**
转载时请注明作者[KiwenLau](http://kiwenlau.com/)以及本文地址：
[http://kiwenlau.com/2015/06/21/python-get-substring/](http://kiwenlau.com/2015/06/21/python-get-substring/)
***











