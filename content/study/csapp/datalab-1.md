---
title: "Csapp Datalab 1"
date: 2022-01-10T21:01:20+08:00
draft: false
slug: datalab-1
---

本篇博客主要介绍我自己csapp的第一个实验datalab的第一部分答案。

## data-lab-1答案

### 1.bitXor

这道题主要是使用德摩根定律，通过它不断化简。

`$$\overline{a + b} = \overline{a} \cdot \overline{b}$$`

`$$\overline{a \cdot b} = \overline{a} + \overline{b}$$`

我目前这个答案并没有化简到最简形式，但是符合要求。
~~~
//1
/* 
 * bitXor - x^y using only ~ and & 
 *   Example: bitXor(4, 5) = 1
 *   Legal ops: ~ &
 *   Max ops: 14
 *   Rating: 1
 */
int bitXor(int x, int y) {
  return  ~ ((~ (~ x & y)) & (~ ( x & (~ y))));
}
~~~


### tmin

补码最小值为`$1 \underbrace{0\cdots0}_{31}$`，由于题目限制只使用0到0xff之间的数，所以利用<<左移操作符，将1移动31到第32位处。

~~~
/* 
 * tmin - return minimum two's complement integer 
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 4
 *   Rating: 1
 */
int tmin(void) {
  return 2<<30;
}
~~~


### isTmax

判断x是不是补码的最大值，2个补码最大值和1相加等于`$\underbrace{1 \cdots 1}_{32}$`，取反后为`$\underbrace{0 \cdots 0}_{32}$`，不过需要注意`$\underbrace{1 \cdots 1}_{32}$`，也会产生这样的效果，所以加个异或排除这一情况。

~~~
//2
/*
 * isTmax - returns 1 if x is the maximum, two's complement number,
 *     and 0 otherwise 
 *   Legal ops: ! ~ & ^ | +
 *   Max ops: 10
 *   Rating: 1
 */
int isTmax(int x) {
  return (!~((x + 1) + x)) ^ (!(~x));
}
~~~

### allOddBits

判断x的奇数位都为1，通过于`$y = \underbrace{1010 \cdots 1010}_{8}$` 的 & 操作，提取出奇数位，再通过~y相加，再加1，若符合要求则为`$\underbrace{0 \cdots 0}_{32}$`。
~~~
/* 
 * allOddBits - return 1 if all odd-numbered bits in word set to 1
 *   where bits are numbered from 0 (least significant) to 31 (most significant)
 *   Examples allOddBits(0xFFFFFFFD) = 0, allOddBits(0xAAAAAAAA) = 1
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 2
 */
int allOddBits(int x) {
  int y = (0xAA) + (0xAA << 8) + (0xAA << 16) + (0xAA << 24);
  return !((x & y) + ~y + 1);
}
~~~

### negate

补码的相反数为补码按位取反，然后加1。

~~~
/* 
 * negate - return -x 
 *   Example: negate(1) = -1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 5
 *   Rating: 2
 */
int negate(int x) {
  return ~x + 1;
}
~~~

### isAsciiDigit

对于这道题，我将x分成2部分来判断。

第一部分，判断是否符合0x30，这个通过上一题的方法即可判断。

第二部分，判断x的前4位是否再0到9这个区间，通过加6，通过他是否进位来判断。

~~~
//3
/* 
 * isAsciiDigit - return 1 if 0x30 <= x <= 0x39 (ASCII codes for characters '0' to '9')
 *   Example: isAsciiDigit(0x35) = 1.
 *            isAsciiDigit(0x3a) = 0.
 *            isAsciiDigit(0x05) = 0.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 15
 *   Rating: 3
 */
int isAsciiDigit(int x) {
  int y1 = 0x30;
  int y2 = (0xff << 24) + (0xff << 16) + (0xff << 8) + 0xf0;
  int y3 = x & y2;
  int z1 = y3 + ~y1 + 1;

  int z2 = ((x & 0xf) + 0x6) & 0x10;
  return !(z1 | z2);
}
~~~

### conditial

这道题只需要做到x == 0 时 w3 == `$\underbrace{0 \cdots 0}_{32}$` 且 w4 == `$\underbrace{1 \cdots 1}_{32}$` ，x > 0 时  w3 == `$\underbrace{1 \cdots 1}_{32}$` 且 w4 == `$\underbrace{0 \cdots 0}_{32}$`。

~~~
/* 
 * conditional - same as x ? y : z 
 *   Example: conditional(2,4,5) = 4
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 16
 *   Rating: 3
 */
int conditional(int x, int y, int z) {
  int w1 = !x;
  int w2 = (0xff << 24) + (0xff << 16) + (0xff << 8) + 0xff;
  int w3 = w1 + w2;
  int w4 = !w1 + w2;

  return (y & w3) + (z & w4);
}
~~~

### isLessOrEqual

x <= y 也就是 x - y <= 0，x - y 的最高位若为1，则证明它小于0，但是我们需要考虑它们相减溢出的情况，所以我们选择将他们都除以2，但保存他们的第一位，当出现做高位为0时，就开始判断 x/2 和 y/2 是否相等，如果相等进而判断先前保存的 x  和 y 的 首位，由于只有当 x 首位为 1 而 y 首位为 0 时 x > y, 所以通过异或操作来排除它。 

~~~
/* 
 * isLessOrEqual - if x <= y  then return 1, else return 0 
 *   Example: isLessOrEqual(4,5) = 1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 24
 *   Rating: 3
 */
int isLessOrEqual(int x, int y) {
  int x1 = x & 1;
  int y1 = y & 1;

  int x2 = x >> 1;
  int y2 = y >> 1;

  int z1 = 8 << 28;
  int z2 = x2 + 1 + ~y2;
  int z3 = !!(z2 & z1);

  int z4 = ! z2;
  int z5 = 1 ^ (x1 & ~y1);
  return z3 | (z4 & z5);
}
~~~