---
title: "leetcode-模拟131"
date: 2026-08-08T11:31:16+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 移除十进制表示中的所有零

给你一个正整数n。

返回一个整数，该整数是将 n 的十进制表示中所有的零都移除后得到的结果。


```
impl Solution {
    pub fn remove_zeros(n: i64) -> i64 {
        let mut ans = 0;
        let mut pow10 = 1;
        let mut n = n;
        while n > 0 {
            let d = n % 10;
            if d != 0 {
                ans += d * pow10;
                pow10 *= 10;
            }
            n /= 10;
        }
        ans
    }
}
```
