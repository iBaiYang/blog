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




<br/><br/><br/><br/><br/>
## 参考资料

Unicode 和 UTF-8 的关系与区别 <https://blog.csdn.net/mahoon411/article/details/115586090>

Unicode 和 UTF-8 有什么区别？ <https://www.zhihu.com/question/23374078/answer/69732605>

最为透彻的utf-8、unicode详解 <https://blog.csdn.net/TCF_JingFeng/article/details/80134600>
