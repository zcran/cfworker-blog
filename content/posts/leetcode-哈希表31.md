---
title: "leetcode-哈希表31"
date: 2026-10-09T20:07:57+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 可被 K 整除的最小整数

给定正整数 k ，你需要找出可以被 k 整除的、仅包含数字 1 的最 小 正整数 n 的长度。

返回 n 的长度。如果不存在这样的 n ，就返回-1。

注意： n 可能不符合 64 位带符号整数。


```
impl Solution {
    pub fn smallest_repunit_div_by_k(k: i32) -> i32 {
        // 只保留余数：n 可能远超 64 位，但 (n*10+1) % k 只依赖 n % k。
        // 若 k 与 2、5 互质则必有解，最多 k 步内余数循环；否则无解。
        let mut rem = 1 % k;
        for len in 1..=k {
            if rem == 0 {
                return len;
            }
            rem = (rem * 10 + 1) % k;
        }
        -1
    }
}
```
