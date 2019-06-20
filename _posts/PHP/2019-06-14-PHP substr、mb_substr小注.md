---
layout: post
categories: PHP
title: PHP substr、mb_substr小注
meta: substr、mb_substr小注
---
* content
{:toc}

### 正文

在一次编程中，字符串是汉字，需要截取一部分字符，当时使用了用的最频繁的substr()，写法是：substr($str, 0, -1)；
写完后有一瞬的感觉：涉及到汉字，应该用mb系函数，这里应该就是mb_substr($str, 0, -1)。

当时在赶任务，也没有多想。过了两天，需要联合测试时发现数据异常，没有立刻再想到。因为是异步编程，几乎一行一行打断点输出，
最后才确定问题出在这里，汉字截取应该用mb_substr()，改完后立刻好了。

花费了大量时间，以后碰到汉字处理就果断查清楚并使用mb系列函数。碰到感觉可能存在问题的地方，就赶快调查清楚，不要把问题留在以后，
因为在一大块代码中找问题，你需要一行一行过代码！


<br/><br/><br/><br/><br/>
### 参考资料

substr <https://www.php.net/manual/zh/function.substr.php>

mb_substr <https://www.php.net/manual/zh/function.mb-substr.php>

<https://www.runoob.com/php/func-string-mb_substr.html>

