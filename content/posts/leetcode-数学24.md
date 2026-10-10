---
title: "leetcode-数学24"
date: 2026-10-07T20:39:29+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 将整数转换为两个无零整数的和

「无零整数」是十进制表示中 不含任何 0 的正整数。

给你一个整数 n，请你返回一个 由两个整数组成的列表 [a, b]，满足：

a 和 b 都是无零整数
a + b = n

题目数据保证至少有一个有效的解决方案。

如果存在多个有效解决方案，你可以返回其中任意一个。


```
impl Solution {
    /// 返回两个「无零整数」a、b，使得 a + b = n。
    ///
    /// 无零整数指十进制表示中不含 0 的正整数。
    /// 从小到大枚举 a，检查 a 和 n - a 是否都不含 0，找到即返回。
    /// 题目保证至少存在一组解，因此循环内必能返回。
    pub fn get_no_zero_integers(n: i32) -> Vec<i32> {
        for a in 1..n {
            let b = n - a;
            if Self::has_no_zero(a) && Self::has_no_zero(b) {
                return vec![a, b];
            }
        }
        unreachable!()
    }

    /// 判断整数 x 的十进制表示中是否不含 0（逐位取模，避免字符串分配）。
    #[inline]
    fn has_no_zero(mut x: i32) -> bool {
        while x > 0 {
            if x % 10 == 0 {
                return false;
            }
            x /= 10;
        }
        true
    }
}
```
