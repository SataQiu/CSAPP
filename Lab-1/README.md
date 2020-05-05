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
