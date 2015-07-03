title: tr与sed命令:将换行符替换为空格
date: 2015-06-23 18:56:27
tags: [Linux命令, tr, sed]
---
处理文本时需要将换行符替换为空格，若使用sed命令会比较麻烦，而使用tr命令非常方便。

![](/image/150623/tr-sed-newline.png)

**输入文本(country.txt)**
```plain
China
America
France
German
```

**sed命令**(参考[sed命令替换换行符](http://my.oschina.net/shelllife/blog/118337))
```sh
cat country.txt | sed ':label;N;s/\n/ /;b label'
```

**tr命令**
```sh
cat country.txt | tr "\n" " "
```

**两个命令输出一致，但是sed命令的输出结尾有换行符，而tr命令的输出结尾没有换行符**
```plain
China America France German
```

***
**版权声明**
转载时请注明作者[KiwenLau](http://kiwenlau.com/)以及本文地址：[http://kiwenlau.com/2015/06/23/tr-sed-newline/](http://kiwenlau.com/2015/06/23/tr-sed-newline/)
***
