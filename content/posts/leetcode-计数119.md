---
title: "leetcode-计数119"
date: 2026-08-01T10:40:57+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 出现次数能被 K 整除的元素总和

给你一个整数数组 nums 和一个整数 k。

请返回一个整数，表示 nums 中所有其 出现次数 能被 k 整除的元素的总和；如果没有这样的元素，则返回 0 。

注意： 若某个元素在数组中的总出现次数能被 k 整除，则它在求和中会被计算 恰好 与其出现次数相同的次数。

元素 x 的 出现次数 指它在数组中出现的次数。


```
impl Solution {
    /// 返回 nums 中出现次数能被 k 整除的所有元素之和。
    /// 每个满足条件的元素贡献 num * count 到总和。
    pub fn sum_divisible_by_k(nums: Vec<i32>, k: i32) -> i32 {
        use std::collections::HashMap;

        let mut freq = HashMap::new();
        for &num in &nums {
            *freq.entry(num).or_insert(0) += 1;
        }

        freq.iter()
            .filter(|(_, c)| *c % k == 0)
            .map(|(&num, c)| num * *c)
            .sum()
    }
}
```
