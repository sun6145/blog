---
title: hexo中遇到的坑
tags:
  - hexo搭建
  - null
date: 2018-06-01 15:53:02
categories: hexo
top:
---

# hexo搭建过程中遇到的问题

<!-- more-->

# 在使用hexo g生成文章的时候，报错如下:
> Template render error: (unknown path) [Line 31, Column 21]
expected variable end

- 问题原因:
> 当文章中有}}时,且这两个括号未被代码块包含，解析会出问题



## flow图的语法:

> tag=>type: content:>url  

## 类型

1. start
2. end
3. operation
4. subroutine
5. condition
6. inputoutput

## 连接

连接元素阶段的语法就简单多了，直接用->来连接两个元素，需要注意的是condition类型，因为他有yes和no两个分支，所以要写成 

> c2(yes)->io->e  
>
> c2(no)->op2->e 

```
st=>start: Start|past:>http://www.google.com[blank]
e=>end: End:>http://www.google.com
op1=>operation: My Operation|past
op2=>operation: Stuff|current
sub1=>subroutine: My Subroutine|invalid
cond=>condition: Yes 
or No?|approved:>http://www.baidu.com
c2=>condition: Good idea|rejected
io=>inputoutput: catch something...|request

st->op1(right)->cond
cond(yes, right)->c2
cond(no)->sub1(left)->op1
c2(yes)->io->e
c2(no)->op2->e
```

```flow
st=>start: Start|past:>http://www.google.com[blank]
e=>end: End:>http://www.google.com
op1=>operation: My Operation|past
op2=>operation: Stuff|current
sub1=>subroutine: My Subroutine|invalid
cond=>condition: Yes 
or No?|approved:>http://www.baidu.com
c2=>condition: Good idea|rejected
io=>inputoutput: catch something...|request

st->op1(right)->cond
cond(yes, right)->c2
cond(no)->sub1(left)->op1
c2(yes)->io->e
c2(no)->op2->e
```

