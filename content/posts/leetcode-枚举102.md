---
title: "leetcode-枚举102"
date: 2026-07-09T10:05:04+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 区间内的兼容数字之和 I

给你两个整数 n 和 k。

如果一个 正 整数 x 同时满足以下两个条件，则称其为 兼容 整数：

abs(n - x) <= k

(n & x) == 0

返回所有 兼容 整数 x 的总和。

注意：

这里，& 表示 按位与 运算符。

整数 i 和 j 之间的 绝对 差定义为 abs(i - j)。


```
impl Solution {
    pub fn sum_of_good_integers(n: i32, k: i32) -> i32 {
        let start = (n - k).max(1);
        let end = n + k;
        let mut ans = 0;

        // 枚举所有满足 |n - x| <= k 的正整数 x
        for x in start..=end {
            // 检查按位与是否为 0
            if (n & x) == 0 {
                ans += x;
            }
        }

        ans
    }
}
```
