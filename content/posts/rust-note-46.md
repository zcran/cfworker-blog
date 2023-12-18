---
title: "Rust Note 46"
date: 2023-06-12T10:35:06+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 颠倒二进制位

颠倒给定的 32 位无符号整数的二进制位。

提示：

请注意，在某些语言（如 Java）中，没有无符号整数类型。在这种情况下，输入和输出都将被指定为有符号整数类型，并且不应影响您的实现，因为无论整数是有符号的还是无符号的，其内部的二进制表示形式都是相同的。
在 Java 中，编译器使用二进制补码记法来表示有符号整数。因此，在 示例 2 中，输入表示有符号整数 -3，输出表示有符号整数 -1073741825。

### C 解法1：
```
// 逐位颠倒，n(logn)
class Solution {
public:
    uint32_t reverseBits(uint32_t n) {
        uint32_t rev = 0;
        for (int i = 0; i < 32 && n > 0; ++i) {
            rev |= (n & 1) << (31 - i);
            n >>= 1;
        }
        return rev;
    }
};
```

### C解法2:
```
 // 分治
class Solution {
private:
    const uint32_t M1 = 0x55555555; // 01010101010101010101010101010101
    const uint32_t M2 = 0x33333333; // 00110011001100110011001100110011
    const uint32_t M4 = 0x0f0f0f0f; // 00001111000011110000111100001111
    const uint32_t M8 = 0x00ff00ff; // 00000000111111110000000011111111

public:
    uint32_t reverseBits(uint32_t n) {
        n = n >> 1 & M1 | (n & M1) << 1;
        n = n >> 2 & M2 | (n & M2) << 2;
        n = n >> 4 & M4 | (n & M4) << 4;
        n = n >> 8 & M8 | (n & M8) << 8;
        return n >> 16 | n << 16;
    }
};
```

### Rust 解法：
```
impl Solution {
    pub fn reverse_bits(mut x: u32) -> u32 {
        (0..32).map(|_| {
            let res = x % 2;
            x = x / 2;
            res
        }).fold(0, |last, curr| last * 2 + curr)
    }
}
```

```
定义一个函数reverse_bits，其形参x为待翻转二进制位序列的无符号整数。
在函数内部，首先进行循环操作，循环次数为32次，即一共需要翻转32个位。
在每次循环中，使用取模运算和整除运算得到当前位的值。
将当前位的值保存在一个临时变量res中。
将当前位从整数x中去除，即将x除以2。
将临时变量res添加到新的无符号整数中，同时需要将这个新的无符号整数左移一位（乘以2），保证下一次循环时仍然能够添加新的位到正确位置。
循环结束后，返回新的无符号整数作为函数的结果。
这段代码中主要使用了map和fold这两个迭代器方法来实现上述的逐位操作和组合操作。
```