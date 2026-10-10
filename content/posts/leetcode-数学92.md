---
title: "leetcode-数学92"
date: 2026-10-07T20:39:34+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 判定好整数

给你一个正整数 n。

令 digitSum 表示 n 的各位数字之和，令 squareSum 表示 n 的各位数字平方之和。

如果一个整数满足 squareSum - digitSum >= 50，则称它是 好整数 。

如果 n 是好整数，返回 true；否则，返回 false。




```
impl Solution {
    pub fn check_good_integer(n: i32) -> bool {
        // 逐位累加 d*d - d，等价于 squareSum - digitSum，
        // 提前达到 50 即可短路返回
        let mut n = n;
        let mut diff = 0;
        while n > 0 {
            let d = n % 10;
            diff += d * d - d;
            if diff >= 50 {
                return true;
            }
            n /= 10;
        }
        false
    }
}
```
