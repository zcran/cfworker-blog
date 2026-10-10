---
title: "leetcode-动态规划187"
date: 2026-10-05T20:40:07+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 比特位计数

给定一个非负整数 n ，请计算 0 到 n 之间的每个数字的二进制表示中 1 的个数，并输出一个数组。




```
impl Solution {
    pub fn count_bits(n: i32) -> Vec<i32> {
        // ans[i] = ans[i >> 1] + (i & 1)
        // 即 i 的二进制 1 的个数 = i 右移一位的个数 + 最低位是否为 1
        let n = n as usize;
        let mut ans = vec![0i32; n + 1];
        for i in 1..=n {
            ans[i] = ans[i >> 1] + (i as i32 & 1);
        }
        ans
    }
}
```
