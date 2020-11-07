# 整数溢出

## 一、漏洞成因

整型数据在内存中是一个固定长度的空间，当发生赋值时，可能超出最大值（或最小值），导致整数溢出。

整数溢出本身并没有改写额外内存，但是如果该整型值对应的是缓冲区大小或数值索引时，就会导致潜在的缓冲区溢出，从而导致任意代码执行。

## 二、主要知识

| 类型                       | 字节         | 范围                                                         |
| -------------------------- | ------------ | ------------------------------------------------------------ |
| __int8,bool,char           | 1byte        | 0~127(0~0x7f) -128~-1(0x80~0xff)                             |
| unsigned char              | 1byte        | 0~255(0~0xff)                                                |
| __int16,short              | 2byte(word)  | 0~32767(0~0x7fff) -32768~-1(0x8000~0xffff)                   |
| unsigned short             | 2byte(word)  | 0~65535(0~0xffff)                                            |
| __int32,int,long           | 4byte(dword) | 0~2147483647(0~0x7fffffff) -2147483648~-1(0x80000000~0xffffffff) |
| unsigned int,unsigned long | 4byte(dword) | 0~4294967295(0~0xffffffff)                                   |
| __int64,long long          | 8byte(qword) | 正: 0~0x7fffffffffffffff 负: 0x8000000000000000~0xffffffffffffffff |
| unsigned long long         | 8byte(qword) | 0~0xffffffffffffffff                                         |

## 三、CTF题型分类

*注意：因整数溢出的特殊性，在PWN类型中往往与缓冲区溢出同时考察，此外还可能出现在WEB，RE等题型中。*

### 1、未限制范围

**（1）题型特征**

变量赋值，没有有效地限制其范围。

a、对于有符号数，当同符号的两数相加（乘）时，就可能改变符号位，导致溢出。

b、对于无符号数，当最小数-1时变为最大数，当最大数+1时变为最小数，导致回绕。

**（2）常用解题套路**

a、注意界限，用char型来举例，127+1=-128，-128-1=127，0-1=-1，-1+1=0；

​                         用unsigned char型来举例，0-1=255，255+1=0；

b、注意相等关系，用int型来举例，0x80000000 = -0x80000000；

### 2、错误的类型转换

**（1）题型特征**

参数传递时，被迫类型转换。

a、当将一个较大宽度的数存入一个较小宽度变量中时，高位发生截断。

b、当有符号数作为无符号数使用时，造成数据量的扩大。

**（2）常用解题套路**

a、隐藏的类型，typedef unsigned int size_t；

b、漏洞多发函数，void *memcpy(void *dest, const void *src, size_t n);

​                                 char *strncpy(char *dest, const char *src, size_t n);

​                                 size_t read(int fd, const void* buf, size_t n);

c、特别注意类型的大小，long long > long = int > short > char；

## 四、例题

### 1、网鼎杯-2020-白虎组-恶龙

**（1）题目**

这是一个与史莱姆和恶龙战斗的故事，请战胜三头恶龙，取得flag。张三长老说，试图patch这个程序的人会得到错误的flag…

**（2）解题思路**

有符号数的溢出，从负数溢出到正数，绕过验证条件，实现数值的改写，得到flag。

**（3）[wp](D:\study\challenge\网鼎杯\2020\恶龙\hero.md)**

### 2、攻防世界-PWN新手区-int_overflow

**（1）题目**

这题似乎没有办法溢出，真的么?

**（2）解题思路**

一个size_t到unsigned __int8的隐藏类型转换，导致截断可绕过条件语句，造成栈溢出，改写返回地址。

**（3）[wp](D:\study\challenge\ADworld\pwn\exercise\int_overflow\int_overflow.md)**

### 3、ZCTF-2016-note3

**（1）题目**

无。

**（2）解题思路**

使用0x8000000000000000 = -0x8000000000000000绕过限制条件，然后就是正常的unlink。

**（3）[wp](https://my.oschina.net/u/4323572/blog/3566586)**

## 五、备注

1、是否可以程序化识别？是否可以通过查询题型特征数据库方式人工识别？

整数溢出程序化识别较难实现，需要通过经验进行识别，主要是特征数据库方式人工识别。

2、是否可以形成通用EXP？

无法形成通用EXP。