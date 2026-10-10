---
title: "leetcode-数学91"
date: 2026-10-07T20:39:34+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 计算数字频率得分

给你一个整数 n。

n 的 得分 定义为：对所有 不同 数字 d，计算 d * freq(d) 的总和，其中 freq(d) 表示数字 d 在 n 中出现的次数。

返回一个整数，表示 n 的得分。


```
impl Solution {
    pub fn digit_frequency_score(n: i32) -> i32 {
        // 每个数字 d 对总和的贡献恰好是 d * freq(d)，
        // 因此只需逐位累加各位数字即可
        let mut n = n;
        let mut ans = 0;
        while n > 0 {
            ans += n % 10;
            n /= 10;
        }
        ans
    }
}
```
