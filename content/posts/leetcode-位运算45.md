---
title: "leetcode-位运算45"
date: 2026-09-24T10:33:37+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 奇偶位数

给你一个 正 整数 n 。

用 even 表示在 n 的二进制形式（下标从 0 开始）中值为 1 的偶数下标的个数。

用 odd 表示在 n 的二进制形式（下标从 0 开始）中值为 1 的奇数下标的个数。

请注意，在数字的二进制表示中，位下标的顺序 从右到左。

返回整数数组 answer ，其中 answer = [even, odd] 。


```
impl Solution {
    pub fn even_odd_bit(n: i32) -> Vec<i32> {
        // 依次取出 n 的最低位，按位下标奇偶分别累加 1 的个数。
        let mut res = vec![0, 0]; // res[0] 统计偶数下标，res[1] 统计奇数下标
        let mut n = n;
        let mut idx = 0;
        while n > 0 {
            res[idx] += n & 1;
            n >>= 1;
            idx ^= 1; // 在 0 和 1 之间切换，对应下标的奇偶
        }
        res
    }
}
```
