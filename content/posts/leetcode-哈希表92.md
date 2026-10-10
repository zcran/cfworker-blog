---
title: "leetcode-哈希表92"
date: 2026-10-09T20:08:01+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 统计等间距出现整数数目 II

给你一个整数数组 nums。

如果一个整数 x 满足以下条件，则被称为 特别 的：

x 在 nums 中 至少出现三次。

x 的 所有 出现，在 nums 中都是 等间隔 的。换句话说，如果 x 的所有出现位置的下标为 i1 < i2 < ... < im，那么 i2 - i1 = i3 - i2 = ... = im - iˇ(m-1)。

返回 nums 中 不同 特别整数的数量。


```
use std::collections::HashMap;

impl Solution {
    pub fn count_special_integers(nums: Vec<i32>) -> i32 {
        // 记录每个值出现的所有下标
        let mut positions: HashMap<i32, Vec<usize>> = HashMap::new();
        for (i, &v) in nums.iter().enumerate() {
            positions.entry(v).or_default().push(i);
        }

        // 至少出现三次，且相邻出现位置的间隔全部相等
        positions
            .values()
            .filter(|p| {
                p.len() >= 3 && p.windows(2).all(|w| w[1] - w[0] == p[1] - p[0])
            })
            .count() as i32
    }
}
```
