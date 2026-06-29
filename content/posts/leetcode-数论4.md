---
title: "leetcode-数论4"
date: 2026-06-20T11:09:54+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 卡牌分组

给定一副牌，每张牌上都写着一个整数。

此时，你需要选定一个数字 X，使我们可以将整副牌按下述规则分成 1 组或更多组：

每组都有 X 张牌。
组内所有的牌上都写着相同的整数。

仅当你可选的 X >= 2 时返回 true。


```
use std::collections::HashMap;

impl Solution {
    /// 判断能否将牌分成若干组，每组大小 X >= 2，且组内数字相同
    ///
    /// 核心思路：统计每个数字出现的次数，求所有次数的最大公约数（GCD）
    /// 如果 GCD >= 2，则可以将牌分成 GCD 张一组
    pub fn has_groups_size_x(deck: Vec<i32>) -> bool {
        // ---------- 1. 统计每个数字的出现次数 ----------
        let mut counts = HashMap::new();
        for &num in &deck {
            *counts.entry(num).or_insert(0) += 1;
        }

        // ---------- 2. 求所有次数的最大公约数 ----------
        // 使用 fold 累积计算所有次数的 GCD
        let gcd = counts
            .values()
            .fold(0, |acc, &cnt| Self::gcd(acc, cnt));

        // 如果 GCD >= 2，说明可以分成每组 gcd 张
        gcd >= 2
    }

    /// 计算两个数的最大公约数（欧几里得算法）
    #[inline]
    fn gcd(a: i32, b: i32) -> i32 {
        if b == 0 {
            a
        } else {
            Self::gcd(b, a % b)
        }
    }
}
```
