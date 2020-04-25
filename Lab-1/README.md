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

最小的二进制数，即首位为 1，其他位为 0

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

##### 2. isTmax

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
