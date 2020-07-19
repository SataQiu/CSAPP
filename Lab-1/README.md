### CSAPP: 数据实验

#### 整数运算

##### 1. bitXor

题意：使用 ~ & 操作实现两数异或。

首先，观察两数相 & 的规律：

```
0 & 0 = 0
0 & 1 = 0
1 & 0 = 0
1 & 1 = 1
```

再观察，两数先取反然后相 & 的规律：

```
~0 & ~0 = 1
~0 & ~1 = 0
~1 & ~0 = 0
~1 & ~1 = 0
```

继续观察，对上述两种运算所得结果，分别取反再相 & 的规律：

```
~0 & ~1 = 0
~0 & ~0 = 1
~0 & ~0 = 1
~1 & ~0 = 0
```

容易看出，上述运算所得结果恰好为两数异或的结果，本题得解：

```cpp
/* 
 * bitXor - x^y using only ~ and & 
 *   Example: bitXor(4, 5) = 1
 *   Legal ops: ~ &
 *   Max ops: 14
 *   Rating: 1
 */
int bitXor(int x, int y) {
  int a = x & y;
  int b = ~x & ~y;
  return ~a & ~b;
}
```

##### 2. tmin

题意：求最小的二进制数，即首位为 1，其他位为 0，允许使用 ! ~ & ^ | + << >>

```cpp
/* 
 * tmin - return minimum two's complement integer 
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 4
 *   Rating: 1
 */
int tmin(void) {
  return 1<<31;
}
```

##### 3. isTmax

题意：判断一个整数是否是最大的二进制整数，允许使用 ! ~ & ^ | +。

以 4 位二进制数为例，最大的整数形式为 0111，将其加 1，结果为 1000，
将 1000 取反，得到 0111，恰好为原数 0111，因此最大的整数具有如下特性：

```
~(x+1)^x == 0
```

这里还有一个特例需要考虑，即当 x == -1 时，其二进制形式为 1111，加 1 后会溢出，变成 0000，
取反后的结果又会成为 1111，1111^1111 == 0，满足上式，但并非最大的整数，需要剔除，
而判断一个数是否为 -1，可以取反后判断结果是否为 0，为 0 则原数为 -1，综上：

```cpp
/*
 * isTmax - returns 1 if x is the maximum, two's complement number,
 *     and 0 otherwise 
 *   Legal ops: ! ~ & ^ | +
 *   Max ops: 10
 *   Rating: 1
 */
int isTmax(int x) {
  int n = !(~x); // 判断 x 是否是 -1
  return !(~(x+1)^x|n);
}
```

##### 4. allOddBits

题意：判断某个整数是否奇数位全为 1，允许使用 ! ~ & ^ | + << >>

首先，构造奇数位为 1 的掩码， 即 10101010...10，转为 16 进制，即为 0xAAAAAAAA

将目标数与掩码相与，再与掩码进行异或，如果异或结果为 0，则可判定目标数的奇数位全为 1

```cpp
/* 
 * allOddBits - return 1 if all odd-numbered bits in word set to 1
 *   where bits are numbered from 0 (least significant) to 31 (most significant)
 *   Examples allOddBits(0xFFFFFFFD) = 0, allOddBits(0xAAAAAAAA) = 1
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 2
 */
int allOddBits(int x) {
  int mask = 0xAA;           //0xAA
  mask = (mask << 8) + 0xAA; //0xAA AA
  mask = (mask << 8) + 0xAA; //0xAA AA AA
  mask = (mask << 8) + 0xAA; //0xAA AA AA AA
  return !((x & mask)^mask);
}
```

##### 5. negate

题意：求一个整数的相反数，允许使用 ! ~ & ^ | + << >>

根据补码性质，-x = ~x + 1

```cpp
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
```

##### 6. isAsciiDigit

题意：判断某个整数是否是 ASCII 字符，即满足 0x30 <= x <= 0x39，允许使用 ! ~ & ^ | + << >>

根据第 5 题，我们已经知道如何求一个数的相反数，该判断条件可以转化为：x - 0x30 与 0x39 - x 均为非负数

而判断一个数是否为负数，即判断最高位是否为 1，综上：

```cpp
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
  int min = 0x30;
  int max = 0x39;
  return (!((x+(~min+1))>>31)) & (!((max+(~x+1))>>31));
}
```

##### 7. conditional

题意：实现 x ? y : z 条件语句，允许使用 ! ~ & ^ | + << >>

构造掩码，当 x 为 0 时，全 1 掩出 z，当 x 为非 0 时，全 0 掩掉 z，全 1 掩出 y

```cpp
/* 
 * conditional - same as x ? y : z 
 *   Example: conditional(2,4,5) = 4
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 16
 *   Rating: 3
 */
int conditional(int x, int y, int z) {
  int mask = (!x + ~0x00); // if x == 0, mask = 0x00000000; else mask = 0xffffffff
  return ((~mask) & z) | ((mask) & y);
}
```

##### 8. isLessOrEqual

题意：判断 x 是否小于等于 y，允许使用 ~ & ^ | + << >>

- 如果 x，y 符号不同，且 x 为负数，y 为正数，返回 1
- 如果 x，y 符号相同，执行 y - x，即 y + (~x + 1)，判断结果是否是正数

```cpp
/* 
 * isLessOrEqual - if x <= y  then return 1, else return 0 
 *   Example: isLessOrEqual(4,5) = 1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 24
 *   Rating: 3
 */
int isLessOrEqual(int x, int y) {
	int sign_x = (x>>31) & 1;
	int sign_y = (y>>31) & 1;
	int y_x = y + (~x + 1);
	int sign_y_x = (y_x>>31) & 1;
	return (sign_x & !sign_y) | (!(sign_x^sign_y) & !sign_y_x);
}
```

##### 9. logicalNeg

题意：实现逻辑非，x 为 0 返回 0 否则返回 1，允许使用 ~ & ^ | + << >>

如果 x 为 0，那么 -x 依然是 0，二者或操作的结果符号位为 0，0 异或 1 为 1
如果 x 不为 0，那么 -x 不为 0，二者或操作的结果符号位为 1，1 异或 1 为 0

```cpp
/* 
 * logicalNeg - implement the ! operator, using all of 
 *              the legal operators except !
 *   Examples: logicalNeg(3) = 0, logicalNeg(0) = 1
 *   Legal ops: ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 4 
 */
int logicalNeg(int x) {
  return (((x | (~x + 1)) >> 31) & 1) ^ 1;
}
```

##### 10. howManyBits

题意：求至少需要多少位二进制表示给定的整数，允许使用 ! ~ & ^ | + << >>

统一负数为正数方式处理，二分查找最高位为 1，最后加上符号位

```cpp
/* howManyBits - return the minimum number of bits required to represent x in
 *             two's complement
 *  Examples: howManyBits(12) = 5
 *            howManyBits(298) = 10
 *            howManyBits(-5) = 4
 *            howManyBits(0)  = 1
 *            howManyBits(-1) = 1
 *            howManyBits(0x80000000) = 32
 *  Legal ops: ! ~ & ^ | + << >>
 *  Max ops: 90
 *  Rating: 4
 */
int howManyBits(int x) {
  int sign = (x >> 31) & 1;
  int f = ~(!sign) + 1;
  int of = ~f;
  /*
    * NOTing x to remove the effect of the sign bit.
    * x = x < 0 ? ~x : x
    */
  x = ((f ^ ~x) & of) | ((of ^ x) & f);
  /*
    * We need to get the index of the highest bit 1.
    * Easy to find that if it's even-numbered, `n` will lose the length of 1.
    * But the odd-numvered won't.
    * So let's left shift 1 (for the first 1) to fix this.
    */
  x |= (x << 1);
  int n = 0;
  // Get index with bisection.
  n += (!!(x & (~0 << (n + 16)))) << 4;
  n += (!!(x & (~0 << (n + 8)))) << 3;
  n += (!!(x & (~0 << (n + 4)))) << 2;
  n += (!!(x & (~0 << (n + 2)))) << 1;
  n += !!(x & (~0 << (n + 1)));
  // Add one more for the sign bit.
  return n + 1;
}
```

#### 浮点数运算

##### 1. floatScale2

题意：求浮点数乘 2 后的结果

```cpp
/* 
 * floatScale2 - Return bit-level equivalent of expression 2*f for
 *   floating point argument f.
 *   Both the argument and result are passed as unsigned int's, but
 *   they are to be interpreted as the bit-level representation of
 *   single-precision floating point values.
 *   When argument is NaN, return argument
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
 *   Max ops: 30
 *   Rating: 4
 */
unsigned floatScale2(unsigned uf) {
  int exp = (uf&0x7f800000)>>23;
  int sign = uf&(1<<31);
  if(exp==0) return uf<<1|sign;
  if(exp==255) return uf;
  exp++;
  if(exp==255) return 0x7f800000|sign;
  return (exp<<23)|(uf&0x807fffff);
}
```

##### 2. floatFloat2Int

题意：将浮点数转换为整数

```cpp
/* 
 * floatFloat2Int - Return bit-level equivalent of expression (int) f
 *   for floating point argument f.
 *   Argument is passed as unsigned int, but
 *   it is to be interpreted as the bit-level representation of a
 *   single-precision floating point value.
 *   Anything out of range (including NaN and infinity) should return
 *   0x80000000u.
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
 *   Max ops: 30
 *   Rating: 4
 */
int floatFloat2Int(unsigned uf) {
  int s_    = uf>>31;
  int exp_  = ((uf&0x7f800000)>>23)-127;
  int frac_ = (uf&0x007fffff)|0x00800000;
  if(!(uf&0x7fffffff)) return 0;

  if(exp_ > 31) return 0x80000000;
  if(exp_ < 0) return 0;

  if(exp_ > 23) frac_ <<= (exp_-23);
  else frac_ >>= (23-exp_);

  if(!((frac_>>31)^s_)) return frac_;
  else if(frac_>>31) return 0x80000000;
  else return ~frac_+1;
}
```

##### 3. floatPower2

题意：求 pow(2.0, x) 的值

```cpp
/* 
 * floatPower2 - Return bit-level equivalent of the expression 2.0^x
 *   (2.0 raised to the power x) for any 32-bit integer x.
 *
 *   The unsigned value that is returned should have the identical bit
 *   representation as the single-precision floating-point number 2.0^x.
 *   If the result is too small to be represented as a denorm, return
 *   0. If too large, return +INF.
 * 
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. Also if, while 
 *   Max ops: 30 
 *   Rating: 4
 */
unsigned floatPower2(int x) {
  int INF = 0xff<<23;
  int exp = x + 127;
  if(exp <= 0) return 0;
  if(exp >= 255) return INF;
  return exp << 23;
}
```

#### 实验结果展示

```sh
[root@dev Lab-1]# make
[root@dev Lab-1]# ./btest
Score	Rating	Errors	Function
 1	1	0	bitXor
 1	1	0	tmin
 1	1	0	isTmax
 2	2	0	allOddBits
 2	2	0	negate
 3	3	0	isAsciiDigit
 3	3	0	conditional
 3	3	0	isLessOrEqual
 4	4	0	logicalNeg
 4	4	0	howManyBits
 4	4	0	floatScale2
 4	4	0	floatFloat2Int
 4	4	0	floatPower2
Total points: 36/36
```

#### 编译错误处理

在 64 位 Ubuntu 系统运行 `make` 命令，可能发生如下报错：

```sh
gcc -O -Wall -m32 -lm -o btest bits.c btest.c decl.c tests.c
In file included from btest.c:16:0:
/usr/include/stdio.h:27:10: fatal error: bits/libc-header-start.h: No such file or directory
 #include <bits/libc-header-start.h>
          ^~~~~~~~~~~~~~~~~~~~~~~~~~
compilation terminated.
In file included from decl.c:1:0:
/usr/include/stdio.h:27:10: fatal error: bits/libc-header-start.h: No such file or directory
 #include <bits/libc-header-start.h>
          ^~~~~~~~~~~~~~~~~~~~~~~~~~
compilation terminated.
In file included from /usr/lib/gcc/x86_64-linux-gnu/7/include-fixed/limits.h:194:0,
                 from /usr/lib/gcc/x86_64-linux-gnu/7/include-fixed/syslimits.h:7,
                 from /usr/lib/gcc/x86_64-linux-gnu/7/include-fixed/limits.h:34,
                 from tests.c:3:
/usr/include/limits.h:26:10: fatal error: bits/libc-header-start.h: No such file or directory
 #include <bits/libc-header-start.h>
          ^~~~~~~~~~~~~~~~~~~~~~~~~~
compilation terminated.
Makefile:11: recipe for target 'btest' failed
make: *** [btest] Error 1
```

这是缺少 32 位编译库导致的，需要执行如下命令手动安装：

```sh
apt-get install gcc-multilib
```

如果你使用的是 CentOS 系统，可以使用如下命令安装相关 32 位库：

```sh
yum install glibc-devel.i686 libgcc.i686
```
