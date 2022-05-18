---
layout: post
categories: IT技术
title: Unicode 和 UTF-8 是什么
meta: Unicode 和 UTF-8 是什么
---
* content
{:toc}
  
## 正文

Unicode 是“字符集”，UTF-8 是“编码规则”。

其中，“字符集”的意思是：为每一个“字符‘”分配一个唯一的 ID（学名为码位 / 码点 / Code Point）；
“编码规则”的意思是：将“码位”转换为字节序列的规则（编码/解码 可以理解为 加密/解密 的过程）。

广义的Unicode是一个标准，定义了一个“字符集”以及一系列的“编码规则”，即Unicode“字符集”和UTF-8等“编码规则”。

Unicode字符集为每一个字符分配一个码位。UTF-8，是一套以8位为一个编码单位的可变长编码，会将一个码位编码为1到4个字节。

### utf8mb4

MySQL在5.5.3之后增加了这个utf8mb4的编码，mb4就是most bytes 4的意思，专门用来兼容四字节的unicode。
好在utf8mb4是utf8的超集，除了将编码改为utf8mb4外不需要做其他转换。当然，为了节省空间，一般情况下使用utf8也就够了。

最初的 UTF-8 格式使用一至六个字节，最大能编码 31 位字符。
最新的 UTF-8 规范只使用一到四个字节，最大能编码21位，正好能够表示所有的 17个 Unicode 平面。 
utf8 是 Mysql 中的一种字符集，只支持最长三个字节的 UTF-8字符，也就是 Unicode 中的基本多文本平面。

那上面说了既然utf8能够存下大部分中文汉字,那为什么还要使用utf8mb4呢? 
原来mysql支持的 utf8 编码最大字符长度为 3 字节，如果遇到 4 字节的宽字符就会插入异常了。
三个字节的 UTF-8 最大能编码的 Unicode 字符是 0xffff，也就是 Unicode 中的基本多文种平面(BMP)。
也就是说，任何不在基本多文本平面的 Unicode字符，都无法使用 Mysql 的 utf8 字符集存储。
包括 Emoji 表情(Emoji 是一种特殊的 Unicode 编码，常见于 ios 和 android 手机上)，和很多不常用的汉字，
以及任何新增的 Unicode 字符等等。

utf8mb4对应的**排序字符集**有 utf8mb4_unicode_ci、utf8mb4_general_ci。

ci 是 case insensitive, 即 "大小写不敏感", a 和 A 会在字符判断中会被当做一样的。

* 准确性：
1. utf8mb4_unicode_ci是基于标准的Unicode来排序和比较，能够在各种语言之间精确排序
2. utf8mb4_general_ci没有实现Unicode排序规则，在遇到某些特殊语言或者字符集，排序结果可能不一致。
3. 但是，在绝大多数情况下，这些特殊字符的顺序并不需要那么精确。
        
* 性能
1. utf8mb4_general_ci在比较和排序的时候更快
2. utf8mb4_unicode_ci在特殊情况下，Unicode排序规则为了能够处理特殊字符的情况，实现了略微复杂的排序算法。
3. 但是在绝大多数情况下发，不会发生此类复杂比较。相比选择哪一种collation，使用者更应该关心字符集与排序规则在db里需要统一。

总而言之，utf8mb4_general_ci 和 utf8mb4_unicode_ci 是我们最常使用的排序规则。
utf8mb4_general_ci 校对速度快，但准确度稍差。
utf8mb4_unicode_ci 准确度高，但校对速度稍慢，两者都不区分大小写。
这两个选哪个视自己情况而定，还是那句话尽可能保持db中的字符集和排序规则的统计。




<br/><br/><br/><br/><br/>
## 参考资料

Unicode 和 UTF-8 的关系与区别 <https://blog.csdn.net/mahoon411/article/details/115586090>

Unicode 和 UTF-8 有什么区别？ <https://www.zhihu.com/question/23374078/answer/69732605>

最为透彻的utf-8、unicode详解 <https://blog.csdn.net/TCF_JingFeng/article/details/80134600>

Mysql---utf8、utf8mb4、utf8mb4_unicode_ci、utf8mb4_general_ci区别 <https://blog.csdn.net/qq_41653753/article/details/107706711>

mysql中的utf8mb4、utf8mb4_unicode_ci、utf8mb4_general_ci <https://blog.csdn.net/Ezerbel/article/details/111566683>

