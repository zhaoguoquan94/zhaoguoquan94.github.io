---
author: zhaoguoquan
comments: true
date: 2015-01-31 15:34:20+00:00
layout: post
slug: '%e8%a7%a3%e5%af%86python%e4%b8%ad%e7%9a%84unicode'
title: 解密Python中的Unicode
wordpress_id: 63
categories:
- 未分类
---












# 解密Python中的Unicode


仓库:[https://github.com/zhaoguoquan94/unicode_in_python](https://github.com/zhaoguoquan94/unicode_in_python)
<table rules="none" frame="void" class="docinfo" >
<tbody valign="top" >
<tr class="field" >
作者:

<td class="field-body" >Kumar McMillan
</td>
</tr>
<tr class="field" >
译者:

<td class="field-body" >赵国铨
</td>
</tr>
<tr class="field" >
地点:

<td class="field-body" >PyCon 2008, 芝加哥
</td>
</tr>
<tr class="field" >
URL:

<td class="field-body" >[http://farmdev.com/talks/unicode/](http://farmdev.com/talks/unicode/)
</td>
</tr>
<tr class="field" >
Source:

<td class="field-body" >[https://github.com/kumar303/unicode-in-python](https://github.com/kumar303/unicode-in-python)
</td>
</tr>
<tr class="field" >
Source:

<td class="field-body" >[https://github.com/zhaoguoquan94/unicode_in_python](https://github.com/zhaoguoquan94/unicode_in_python)
</td>
</tr>
</tbody>
</table>








# 这是什么?



    
    UnicodeDecodeError: 'ascii' codec
    can't decode byte 0xc4 in position
    10: ordinal not in range(128)
    





	
  * 以前见过这个异常吗?

	
  * 以前见过这个异常并且解决了这个问题?

	
  * 这的确是一个烦人的错误



	
  * 如果之前你没有见过这个异常,而且以后想学习python

	
  * 如果你之前见过这个异常而且不知道怎么解决

	
  * 那么这个talk就是为你准备的

	
  * 如果你不知道Python在内部为你做了什么,那么这的确是一个很具有迷惑性的错误.这个talk会解决这个谜题.










# 概述





	
  * 探秘python中的字符串

	
  * Unicode的魔力

	
  * Python 2中的Unicode如何使用

	
    * 基础概念

	
    * 示例代码




	
  * Python 3中的Unicode简介

	
  * 提问环节

	
  * 纠错环节










# Python中为什么使用unicode





	
  * 处理非英文文本

	
  * 使用第三方模块

	
  * 接受任意编码格式的文字输入

	
  * 你会很爱Unicode

	
  * 不会用的话,你也会很讨厌Unicode










# Web 应用


[![text-lifecycle-web]({{site.url}}/uploads/2015/01/text-lifecycle-web.png)]({{site.url}}/uploads/2015/01/text-lifecycle-web.png)

    
    [form input] => [Python] => [HTML]
    





	
  * 接收输入文本

	
  * 将输入写入html文件










# 与数据库交互


[![text-lifecycle-db]({{site.url}}/uploads/2015/01/text-lifecycle-db.png)]({{site.url}}/uploads/2015/01/text-lifecycle-db.png)

    
    [read from DB] => [Python] => [write to DB]
    





	
  * 接收输入文本

	
  * 将输入写入数据库










# 命令行脚本


[![text-lifecycle-script]({{site.url}}/uploads/2015/01/text-lifecycle-script.png)]({{site.url}}/uploads/2015/01/text-lifecycle-script.png)

    
    [text files] => [Python] => [stdout]
    





	
  * 接收输入文本

	
  * 将输入写入标准输出或其他文件中










# 让我们打开一个UTF-8文件







## Ivan Krstić


上面的就是文件内容




    
    <span style="color: #666666;">>>></span> f <span style="color: #666666;">=</span> <span style="color: #008000;">open</span>(<span style="color: #ba2121;">'/tmp/ivan_utf8.txt'</span>, <span style="color: #ba2121;">'r'</span>)
    <span style="color: #666666;">>>></span> ivan_utf8 <span style="color: #666666;">=</span> f<span style="color: #666666;">.</span>read()
    <span style="color: #666666;">>>></span> ivan_utf8
    <span style="color: #ba2121;">'Ivan Krsti</span><span style="color: #bb6622; font-weight: bold;">\xc4\x87</span><span style="color: #ba2121;">'</span>
    








	
  * Ivan Krstić 是OLPC的首席安全架构师

	
  * 假设你在编辑器中打开了这个文件,并且用UTF-8格式(可能UTF-8并不是默认格式)保存

	
  * 然后你在Python中打开了这个文件













# 这是什么类型?






    
    <span style="color: #666666;">>>></span> ivan_utf8
    <span style="color: #ba2121;">'Ivan Krsti</span><span style="color: #bb6622; font-weight: bold;">\xc4\x87</span><span style="color: #ba2121;">'</span>
    <span style="color: #666666;">>>></span> <span style="color: #008000;">type</span>(ivan_utf8)
    <span style="color: #666666;"><</span><span style="color: #008000;">type</span> <span style="color: #ba2121;">'str'</span><span style="color: #666666;">></span>
    








	
  * 这是一串字节组成的字符串

	
  * 1 字节 = 8 比特

	
  * 1比特不是1就是0










# 文字是被编码的







## Ivan Krstić






    
    <span style="color: #ba2121;">'Ivan Krsti</span><span style="color: #bb6622; font-weight: bold;">\xc4\x87</span><span style="color: #ba2121;">'</span>
    








	
  * 这个字符串是UTF-8编码的

	
  * 一种编码就是一种把数字分配给各个字符的一组规定(译者注:比如ASCII中把65分给字母A,则见到65就认为出现了字母A)

	
  * 我们发现最后一个字符ć竟然占了两个字节

	
  * 其他编码规则可能使用别的的数字来代表ć

	
  * Python支持超过100处编码格式



	
  * ć属于克罗地亚语

	
  * 同样一串文本,使用不同的编码规则就有不同的字节表示













# ASCII编码


<table border="1" class="docutils" > 
<tbody valign="top" >
<tr >

<td >**char**
</td>

<td >I
</td>

<td >v
</td>

<td >a
</td>

<td >n
</td>
</tr>
<tr >

<td >**hex**
</td>

<td >\x49
</td>

<td >\x76
</td>

<td >\x61
</td>

<td >\x6e
</td>
</tr>
<tr >

<td >**decimal**
</td>

<td >73
</td>

<td >118
</td>

<td >97
</td>

<td >110
</td>
</tr>
</tbody>
</table>



	
  * UTF-8 是 ASCII的扩展

	
  * ASCII发布于1963,是美国信息流通的标准编码格式

	
  * 每一个字母由一个字节表示

	
  * 共有128种不同的字符










# ASCII


<table border="1" class="docutils" > 
<tbody valign="top" >
<tr >

<td >**char**
</td>

<td >K
</td>

<td >r
</td>

<td >s
</td>

<td >t
</td>

<td >i
</td>

<td >ć
</td>
</tr>
<tr >

<td >**hex**
</td>

<td >\x4b
</td>

<td >\x76
</td>

<td >\x72
</td>

<td >\x74
</td>

<td >\x69
</td>

<td >nope
</td>
</tr>
<tr >

<td >**decimal**
</td>

<td >75
</td>

<td >118
</td>

<td >114
</td>

<td >116
</td>

<td >105
</td>

<td >sorry
</td>
</tr>
</tbody>
</table>



	
  * ć 不能使用ASCII编码

	
  * ...










# 内置string类型


(Python 2)

    
    <type 'basestring'>
       |
       +--<type 'str'>
       |
       +--<type 'unicode'>
    










# 几个重要的方法







## s.decode(_encoding_)





	
  * 把``<type 'str'>`` 解码为 <type 'unicode'>










## u.encode(_encoding_)





	
  * 把``<type 'unicode'>`` 编码为 <type 'str'>













# 问题


Python中的文本就不能一直保持编码好的状态吗?





## Ivan Krstić






    
    <span style="color: #666666;">>>></span> ivan_utf8
    <span style="color: #ba2121;">'Ivan Krsti</span><span style="color: #bb6622; font-weight: bold;">\xc4\x87</span><span style="color: #ba2121;">'</span>
    <span style="color: #666666;">>>></span> <span style="color: #008000;">len</span>(ivan_utf8)
    <span style="color: #666666;">12</span>
    <span style="color: #666666;">>>></span> ivan_utf8[<span style="color: #666666;">-1</span>]
    <span style="color: #ba2121;">'</span><span style="color: #bb6622; font-weight: bold;">\x87</span><span style="color: #ba2121;">'</span>
    








	
  * 编码好的文本用起来足够方便吗?要是一直使用编码好的,就不会出现各种解码错误了...

	
  * 可是...这样的话,"Ivan Krstić"这串文本的长度真的是12吗?

	
    * 如果使用其他编码格式会有不一样的结果吗




	
  * 这串字符最后一个字母真的是十六进制的87(字母W)吗?我想要的不是"ć"吗?













# Unicode 在更多情况下更为"准确"







## Ivan Krstić






    
    <span style="color: #666666;">>>></span> ivan_utf8
    <span style="color: #ba2121;">'Ivan Krsti</span><span style="color: #bb6622; font-weight: bold;">\xc4\x87</span><span style="color: #ba2121;">'</span>
    <span style="color: #666666;">>>></span> ivan_uni <span style="color: #666666;">=</span> ivan_utf8<span style="color: #666666;">.</span>decode(<span style="color: #ba2121;">'utf-8'</span>)
    <span style="color: #666666;">>>></span> ivan_uni
    <span style="color: #ba2121;">u'Ivan Krsti</span><span style="color: #bb6622; font-weight: bold;">\u0107</span><span style="color: #ba2121;">'</span>
    <span style="color: #666666;">>>></span> <span style="color: #008000;">type</span>(ivan_uni)
    <span style="color: #666666;"><</span><span style="color: #008000;">type</span> <span style="color: #ba2121;">'unicode'</span><span style="color: #666666;">></span>
    
















# Unicode 在更多情况下更为"准确"







## Ivan Krstić






    
    <span style="color: #666666;">>>></span> ivan_uni
    <span style="color: #ba2121;">u'Ivan Krsti</span><span style="color: #bb6622; font-weight: bold;">\u0107</span><span style="color: #ba2121;">'</span>
    <span style="color: #666666;">>>></span> <span style="color: #008000;">len</span>(ivan_uni)
    <span style="color: #666666;">11</span>
    <span style="color: #666666;">>>></span> ivan_uni[<span style="color: #666666;">-1</span>]
    <span style="color: #ba2121;">u'</span><span style="color: #bb6622; font-weight: bold;">\u0107</span><span style="color: #ba2121;">'</span>
    
















# 什么是Unicode






    
    <span style="color: #ba2121;">u'Ivan Krsti</span><span style="color: #bb6622; font-weight: bold;">\u0107</span><span style="color: #ba2121;">'</span>
    








	
  * 是一种与字节编码无关的表示文字的方法

	
  * 全部语言的所有字符都有唯一确定的码点(Code Point)表示

	
  * 支持当今所有常用语言

	
  * 定义了超过一百万的字符以及对应的码点



	
  * 支持...

	
    * 欧洲大多数国家和中东一些地区使用的语言

	
    * 中东地区从右向左的语言

	
    * 亚洲的各种语言

	
    * 数学符号

	
    * ...













# Unicode,完美的化身


如果说 ASCII, UTF-8和其他字节表示的字符串是 "文本"的话...


那么Unicode就是组成文本之本源(译者无力翻译...给出原文自己领悟...If ASCII, UTF-8, and other byte strings are "text" ...then Unicode is "text-ness";




他是文本的一种抽象形式






	
  * [http://en.wikipedia.org/wiki/Platonic_idealism](http://en.wikipedia.org/wiki/Platonic_idealism)










# Unicode is a concept(概念,原则)


<table border="1" class="docutils" > 
<tbody valign="top" >
<tr >

<td >**字母**
</td>

<td >**Unicode码点**
</td>
</tr>
<tr >

<td >ć
</td>

<td >\u0107
</td>
</tr>
</tbody>
</table>



	
  * 想要把它保存在磁盘你需要对其进行编码


<table border="1" class="docutils" > 
<tbody valign="top" >
<tr >

<td colspan="4" >Byte Encodings
</td>
</tr>
<tr >

<td >**letter**
</td>

<td >**UTF-8**
</td>

<td >**UTF-16**
</td>

<td >**Shift-JIS**
</td>
</tr>
<tr >

<td >ć
</td>

<td >\xc4\x87
</td>

<td >\x07\x01
</td>

<td >\x85\xc9
</td>
</tr>
</tbody>
</table>








# Unicode 转码






    
    <span style="color: #666666;">>>></span> ab <span style="color: #666666;">=</span> <span style="color: #008000;">unicode</span>(<span style="color: #ba2121;">'AB'</span>)
    










## UTF-8






    
    <span style="color: #666666;">>>></span> ab<span style="color: #666666;">.</span>encode(<span style="color: #ba2121;">'utf-8'</span>)
    <span style="color: #ba2121;">'AB'</span>
    








	
  * 变长字节表示

	
  * 前128个字符与ASCII相同

	
  * 每个字符可能由1-4字节表示













# Unicode 转码






    
    <span style="color: #666666;">>>></span> ab <span style="color: #666666;">=</span> <span style="color: #008000;">unicode</span>(<span style="color: #ba2121;">'AB'</span>)
    










## UTF-16






    
    <span style="color: #666666;">>>></span> ab<span style="color: #666666;">.</span>encode(<span style="color: #ba2121;">'utf-16'</span>)
    <span style="color: #ba2121;">'</span><span style="color: #bb6622; font-weight: bold;">\xff\xfe</span><span style="color: #ba2121;">A</span><span style="color: #bb6622; font-weight: bold;">\x00</span><span style="color: #ba2121;">B</span><span style="color: #bb6622; font-weight: bold;">\x00</span><span style="color: #ba2121;">'</span>
    








	
  * 变长字节表示

	
  * 每个字符可能由2-4字节表示

	
  * 为双字节表示范围内语言优化













# Unicode 转码







## UTF-32





	
  * 定长表示,速度最快

	
  * 每个码点4个字节

	
  * python不支持...













# Unicode 码表







## Ian Albert的Unicode码表





	
  * 这哥们把所有unicode码表打印到墙上

	
  * 1,114,112 个码点

	
  * 6英尺*12英尺

	
  * 像素22,017 × 42,807













# Unicode 码表


[![unichart-printed]({{site.url}}/uploads/2015/01/unichart-printed.jpg)]({{site.url}}/uploads/2015/01/unichart-printed.jpg)


Ian Albert的 Unicode 码表. 他说这玩意花了20刀,但他确定铁定算错了











# Unicode 码表 50 %


[![unichart-12]({{site.url}}/uploads/2015/01/unichart-12.jpg)]({{site.url}}/uploads/2015/01/unichart-12.jpg)









# Unicode 码表 100 %


[![unichart-50]({{site.url}}/uploads/2015/01/unichart-50.jpg)]({{site.url}}/uploads/2015/01/unichart-50.jpg)









# 把文本解码为unicode





	
  * 几乎全自动

	
  * 在许多第三方模块中经常使用

	
  * Python会尝试为你解码










# Python 魔法






    
    <span style="color: #666666;">>>></span> ivan_uni
    <span style="color: #ba2121;">u'Ivan Krsti</span><span style="color: #bb6622; font-weight: bold;">\u0107</span><span style="color: #ba2121;">'</span>
    <span style="color: #666666;">>>></span> f <span style="color: #666666;">=</span> <span style="color: #008000;">open</span>(<span style="color: #ba2121;">'/tmp/ivan.txt'</span>, <span style="color: #ba2121;">'w'</span>)
    <span style="color: #666666;">>>></span> f<span style="color: #666666;">.</span>write(ivan_uni)
    Traceback (most recent call last):
    <span style="color: #666666;">...</span>
    <span style="color: #d2413a; font-weight: bold;">UnicodeEncodeError</span>: <span style="color: #ba2121;">'ascii'</span> codec can<span style="color: #ba2121;">'t encode character u'</span>\u0107<span style="color: #ba2121;">' in position 10: ordinal not in range(128)</span>
    













# 揭秘Python魔法






    
    <span style="color: #666666;">>>></span> ivan_uni
    <span style="color: #ba2121;">u'Ivan Krsti</span><span style="color: #bb6622; font-weight: bold;">\u0107</span><span style="color: #ba2121;">'</span>
    <span style="color: #666666;">>>></span> f <span style="color: #666666;">=</span> <span style="color: #008000;">open</span>(<span style="color: #ba2121;">'/tmp/ivan.txt'</span>, <span style="color: #ba2121;">'w'</span>)
    <span style="color: #666666;">>>></span> <span style="color: #008000; font-weight: bold;">import</span> <span style="color: #0000ff; font-weight: bold;">sys</span>
    <span style="color: #666666;">>>></span> f<span style="color: #666666;">.</span>write(ivan_uni<span style="color: #666666;">.</span>encode(
    <span style="color: #666666;">...</span>         sys<span style="color: #666666;">.</span>getdefaultencoding()))
    <span style="color: #666666;">...</span>
    Traceback (most recent call last):
    <span style="color: #666666;">...</span>
    <span style="color: #d2413a; font-weight: bold;">UnicodeEncodeError</span>: <span style="color: #ba2121;">'ascii'</span> codec can<span style="color: #ba2121;">'t encode character u'</span>\u0107<span style="color: #ba2121;">' in position 10: ordinal not in range(128)</span>
    













# 卧槽?!




Python2的默认编码是ASCII!











# 能不能改掉默认编码?






    
    sys<span style="color: #666666;">.</span>setdefaultencoding(<span style="color: #ba2121;">'utf-8'</span>)
    








	
  * 我就不能把这玩意放在``sitecustomize.py``中吗?(译者:这个文件是Python设置默认编码以令其能够往控制台等输出格式正确的编码的文件)

	
  * 不行!

	
  * 你的代码在其他Python环境中就不能用了

	
  * 要是改了带来的麻烦更多










# 解决方案





	
  1. **早解码(解码为unicode)**

	
  2. **多用unicode**,在哪都这样

	
  3. **晚编码(编码为其他编码格式)**










# 1. 早解码


解码到``<type 'unicode'>``,越早越好




    
    <span style="color: #666666;">>>></span> <span style="color: #008000; font-weight: bold;">def</span> <span style="color: #0000ff;">to_unicode_or_bust</span>(
    <span style="color: #666666;">...</span>         obj, encoding<span style="color: #666666;">=</span><span style="color: #ba2121;">'utf-8'</span>):
    <span style="color: #666666;">...</span>     <span style="color: #008000; font-weight: bold;">if</span> <span style="color: #008000;">isinstance</span>(obj, <span style="color: #008000;">basestring</span>):
    <span style="color: #666666;">...</span>         <span style="color: #008000; font-weight: bold;">if</span> <span style="color: #aa22ff; font-weight: bold;">not</span> <span style="color: #008000;">isinstance</span>(obj, <span style="color: #008000;">unicode</span>):
    <span style="color: #666666;">...</span>             obj <span style="color: #666666;">=</span> <span style="color: #008000;">unicode</span>(obj, encoding)
    <span style="color: #666666;">...</span>     <span style="color: #008000; font-weight: bold;">return</span> obj
    <span style="color: #666666;">...</span>
    <span style="color: #666666;">>>></span>
    







检测一个对象是否是字符串,如果是字符串却不是unicode,就把它转化成unicode











# 2. 多用unicode






    
    <span style="color: #666666;">>>></span> to_unicode_or_bust(ivan_uni)
    <span style="color: #ba2121;">u'Ivan Krsti</span><span style="color: #bb6622; font-weight: bold;">\u0107</span><span style="color: #ba2121;">'</span>
    <span style="color: #666666;">>>></span> to_unicode_or_bust(ivan_utf8)
    <span style="color: #ba2121;">u'Ivan Krsti</span><span style="color: #bb6622; font-weight: bold;">\u0107</span><span style="color: #ba2121;">'</span>
    <span style="color: #666666;">>>></span> to_unicode_or_bust(<span style="color: #666666;">1234</span>)
    <span style="color: #666666;">1234</span>
    













# 3. 晚编码


当你需要写磁盘或print时,现编码为``<type 'str'>``这种字节表示




    
    <span style="color: #666666;">>>></span> f <span style="color: #666666;">=</span> <span style="color: #008000;">open</span>(<span style="color: #ba2121;">'/tmp/ivan_out.txt'</span>,<span style="color: #ba2121;">'wb'</span>)
    <span style="color: #666666;">>>></span> f<span style="color: #666666;">.</span>write(ivan_uni<span style="color: #666666;">.</span>encode(<span style="color: #ba2121;">'utf-8'</span>))
    <span style="color: #666666;">>>></span> f<span style="color: #666666;">.</span>close()
    













# 捷径







## codecs.open()






    
    <span style="color: #666666;">>>></span> <span style="color: #008000; font-weight: bold;">import</span> <span style="color: #0000ff; font-weight: bold;">codecs</span>
    <span style="color: #666666;">>>></span> f <span style="color: #666666;">=</span> codecs<span style="color: #666666;">.</span>open(<span style="color: #ba2121;">'/tmp/ivan_utf8.txt'</span>, <span style="color: #ba2121;">'r'</span>,
    <span style="color: #666666;">...</span>                 encoding<span style="color: #666666;">=</span><span style="color: #ba2121;">'utf-8'</span>)
    <span style="color: #666666;">...</span>
    <span style="color: #666666;">>>></span> f<span style="color: #666666;">.</span>read()
    <span style="color: #ba2121;">u'Ivan Krsti</span><span style="color: #bb6622; font-weight: bold;">\u0107</span><span style="color: #ba2121;">'</span>
    <span style="color: #666666;">>>></span> f<span style="color: #666666;">.</span>close()
    
















# 捷径







## codecs.open()






    
    <span style="color: #666666;">>>></span> <span style="color: #008000; font-weight: bold;">import</span> <span style="color: #0000ff; font-weight: bold;">codecs</span>
    <span style="color: #666666;">>>></span> f <span style="color: #666666;">=</span> codecs<span style="color: #666666;">.</span>open(<span style="color: #ba2121;">'/tmp/ivan_utf8.txt'</span>, <span style="color: #ba2121;">'w'</span>,
    <span style="color: #666666;">...</span>                 encoding<span style="color: #666666;">=</span><span style="color: #ba2121;">'utf-8'</span>)
    <span style="color: #666666;">...</span>
    <span style="color: #666666;">>>></span> f<span style="color: #666666;">.</span>write(ivan_uni)
    <span style="color: #666666;">>>></span> f<span style="color: #666666;">.</span>close()
    
















# Python 2 Unicode 不兼容性





	
  * 某些第三方模块不兼容unicode

	
    * 报bug啊!




	
  * 某些内置模块不兼容unicode

	
    * csv













# Python 2 Unicode 变通方案





	
  * 临时编码为UTF-8,然后立即解码回来

	
  * CSV文档教你这么做.






    
    <span style="color: #666666;">>>></span> ivan_bytes <span style="color: #666666;">=</span> ivan_uni<span style="color: #666666;">.</span>encode(<span style="color: #ba2121;">'utf-8'</span>)
    <span style="color: #666666;">>>></span> <span style="color: #408080; font-style: italic;"># do stuff</span>
    <span style="color: #666666;">>>></span> ivan_bytes<span style="color: #666666;">.</span>decode(<span style="color: #ba2121;">'utf-8'</span>)
    <span style="color: #ba2121;">u'Ivan Krsti</span><span style="color: #bb6622; font-weight: bold;">\u0107</span><span style="color: #ba2121;">'</span>
    













# BOM





	
  * 有时出现在文件的头部

	
  * 字节顺序标志

	
  * 对UTF-16, UTF-32编码的文件尤其重要

	
    * 大端法Big Endian (高位在前)

	
    * 小短法Little Endian (低位在前)




	
  * UTF-8 BOM 就是告诉你一下:"我是UTF-8文件"

	
    * Windows中很流行













# 检测BOM






    
    <span style="color: #666666;">>>></span> f <span style="color: #666666;">=</span> <span style="color: #008000;">open</span>(<span style="color: #ba2121;">'/tmp/ivan_utf16.txt'</span>,<span style="color: #ba2121;">'r'</span>)
    <span style="color: #666666;">>>></span> sample <span style="color: #666666;">=</span> f<span style="color: #666666;">.</span>read(<span style="color: #666666;">4</span>)
    <span style="color: #666666;">>>></span> sample
    <span style="color: #ba2121;">'</span><span style="color: #bb6622; font-weight: bold;">\xff\xfe</span><span style="color: #ba2121;">I</span><span style="color: #bb6622; font-weight: bold;">\x00</span><span style="color: #ba2121;">'</span>
    








	
  * BOM 可能有 2, 3,或 4 bytes 长










# 检测BOM






    
    <span style="color: #666666;">>>></span> <span style="color: #008000; font-weight: bold;">import</span> <span style="color: #0000ff; font-weight: bold;">codecs</span>
    <span style="color: #666666;">>>></span> (sample<span style="color: #666666;">.</span>startswith(codecs<span style="color: #666666;">.</span>BOM_UTF16_LE) <span style="color: #aa22ff; font-weight: bold;">or</span>
    <span style="color: #666666;">...</span>  sample<span style="color: #666666;">.</span>startswith(codecs<span style="color: #666666;">.</span>BOM_UTF16_BE))
    <span style="color: #666666;">...</span>
    <span style="color: #008000;">True</span>
    <span style="color: #666666;">>>></span> sample<span style="color: #666666;">.</span>startswith(codecs<span style="color: #666666;">.</span>BOM_UTF8)
    <span style="color: #008000;">False</span>
    













# 我需要删掉BOM吗





	
  * 也许吧

	
  * 解码UTF-16时自动删掉BOM

	
  * 但UTF-8不是这样

	
    * _除非_ 你这么写 s.decode('utf-8-sig')

	
      * Python 2.5及以后都是这样
















# 给你一段字节,如何判断其编码?





	
  * 不幸的是,没有一个可靠的方法能这么做

	
  * BOM给你了线索

	
  * HTTP传输中的``Content-type``头一般会告诉你使用了哪个字符集,就像"charset=..."

	
  * 有专门判断字符编码的模块
- [http://chardet.feedparser.org/](http://chardet.feedparser.org/)
- 是Mozilla编码检测的一部分(原文port of Mozilla encoding detection)










# 总结问题





	
  * Python 2默认编码是ASCII

	
  * 文件可能包含一个BOM头

	
  * 并不是所有的Python 2 内部模块支持Unicode

	
  * 无法可靠判断一串字符的编码格式










# 总结问题





	
  * 早解码,多用unicode,晚编码

	
  * 封装那些不支持unicode的模块,自己搞定

	
  * 单元测试不要少了unicode

	
  * 要是猜测编码格式,先猜UTF-8

	
    * 使用BOM头检测编码格式

	
    * 或者使用专业模块chardet.detect()













# Python 3中的Unicode





	
  * 解决了Python2中存在的unicode的问题!

	
  * <type 'str'> 就是一个unicode对象

	
  * 分离开了 <type 'bytes'> 类型

	
  * 所有内置模块都支持Unicode

	
  * 不再使用``u'text'`` 这个语法










# Python 3中的Unicode





	
  * open() 接受一个编码参数, 就像 codecs.open()

	
  * 默认编码格式是UTF-8 而非 ASCII

	
    * woo!




	
  * 依旧需要猜测文件编码格式

	
    * 依旧需要声明编码格式













# 






