---
title: "Rust Note 45"
date: 2023-06-12T10:35:06+08:00
tags: ["rust", "leetcode", "初级算法篇"]
draft: false
---

## 汉明距离

两个整数之间的 汉明距离 指的是这两个数字对应二进制位不同的位置的数目。

给你两个整数 x 和 y，计算并返回它们之间的汉明距离。

### C 解法1：
```
class Solution {
public:
    int hammingDistance(int x, int y) {
        return __builtin_popcount(x ^ y);
    }
};
```

### C解法2:
```
// Brian Kernighan 算法,跳过两个1之间的0,直接对1进行计数
class Solution {
public:
    int hammingDistance(int x, int y) {
        int s = x ^ y, ret = 0;
        while (s) {
            s &= s - 1;
            ret++;
        }
        return ret;
    }
};
```

### Rust 解法：
```
impl Solution {
    pub fn hamming_distance(x: i32, y: i32) -> i32 {
        (x ^ y).count_ones() as i32     
    }
}
```