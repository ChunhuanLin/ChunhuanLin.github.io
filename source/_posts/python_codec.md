---
date: 2017-07-20
title: python codec
tags:
- python
- unicode
- utf-8
---

## 名词解释

**codec** ： 编码（code)和解码(decode)的缩写组合  
**unicode** ：一种对各种符号进行统一编码的规则，即一种规定了各种字符（如中文，英文）到二进制吗映射关系的规则  
**utf-8** ： 按照一定规则将unicode存储到计算机中。由于有的字符对应的unicode字节少（如英文），而有的多（如中文），因此需要一种体系对unicode进行处理，以便能够更加高效的存储到计算机中。  
> 具体参照：[阮一峰的博客]( http://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html)

## python中的编码处理

> 参考链接 [简书](http://www.jianshu.com/p/53bb448fe85b)

Python 默认脚本文件都是**ANSCII**编码的，当文件中有非 ANSCII 编码范围内的字符的时候就要使用"编码指示"来修正一个 module 的定义中，如果.py文件中含有非anscii字符（如中文字符），则需要在第一行或第二行指定编码声明：`# -*- coding=utf-8 -*-` 或者 `#coding=utf-8`  

1. python中有两种字符类型，一种是`str`，另一种是`unicode`，`str`如果要转换为特定的编码类型（如utf-8、gb2312），则必须要先转为`unicode`。也即，所有编码类型的转换都是从`unicode`开始的。  
2. python编码转换函数  
   * 对unicode进行**编码**  
     使用**encode**方法
     ```
     # -*- coding=UTF-8 -*-  
     if __name__ == '__main__':  
        s = u'中国'  
        s_gb = s.encode('gb2312')  
     ```
   * 将unicode编码**还原**为unicode字符  
     使用**decode**方法 或 **unicode**函数  
     ```
     #coding=UTF-8
     if __name__ == '__main__':
        s = u'中国'
        #s为unicode先转为utf-8
        s_utf8 =  s.encode('UTF-8')
     
        # 开始还原
        assert(s_utf8.decode('utf-8') == s)
        # 或者
        # unicode(s_utf8, 'utf-8')
     ```
3. `str`转变编码方式  
   先按python脚本的编码方式进行解码，成为`unicode`，再进行编码，成为自己想要的编码格式。  
   ```
   #coding=UTF-8
   s = '中国'
   s.decode('utf-8').encode('gb2312')
   ```
   如果直接进行`encode`，那么会先以`sys.defaultencoding`指明的方式来解码（默认是`ascii`），这是可能会报错：  
   > UnicodeDecodeError: 'ascii' codec can't decode byte 0xe4 in position 0: ordinal not in range(128)    
   
   这时我们就需要向上述的方式，明确的指示解码方式，解码后再进行编码。也可以**修改**`sys.defaultencodiing`，代码中加入：  
   ```
   import sys 
   reload(sys) # Python2.5 初始化后会删除 sys.setdefaultencoding 这个方法，我们需要重新载入 
   sys.setdefaultencoding('utf-8') 
   ```
4. 判断文件的编码方式  
   使用`chardet`  
   `chardet.detect(rawdata)`
   






    
