title: grep命令匹配多个单词

date: 2015-06-26 09:41:52

tags: [Linux]

---

使用grep命令可以通过匹配单词迅速定位对应的行，但是有时候需要同时匹配多个单词，而且会有不同的匹配要求。本文将通过匹配两个单词作为示例，介绍grep匹配多个单词的方法。


![](/image/150626/grep_word.png)


**输入文本(country.txt)**
```bash
Austria England
Austria Canada
China England
China Canada
```

**1. 匹配同时含两个单词的行**
```sh
cat country.txt | grep Austria | grep England
```

*输出*
```plain
Austria England
```


**2. 匹配两个单词都不存在的行**
```sh
cat country.txt | grep -v Austria | grep -v England
```

*输出*
```plain
China Canada
```

**3. 匹配含有任意一个单词的行**
```sh
cat country.txt | grep -E 'Austria|England'
```

*输出*
```bash
Austria England
Austria Canada
China England
```

**3. 匹配含有其中一个单词但是不含另一个单词的行**
```sh
cat country.txt | grep Austria | grep -v England
```

*输出*
```plain
Austria Canada
```

***
**版权声明**
转载时请注明作者[KiwenLau](http://kiwenlau.com/)以及本文地址：
[http://kiwenlau.com/2015/06/26/150626-grep-multiple-word/](http://kiwenlau.com/2015/06/26/150626-grep-multiple-word/)
***








