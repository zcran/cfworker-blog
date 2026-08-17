---
title: "leetcode-计数117"
date: 2026-08-01T10:40:57+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 数组元素分组

给你一个整数数组 nums 和一个整数 k。

请你判断是否可以将 nums 中的所有元素分成一个或多个组，使得：

每个组 恰好 包含 k 个元素。
每组中的元素 互不相同。
nums 中的每个元素 必须 被分配到 恰好一个 组中。

如果可以完成这样的分组，返回 true；否则，返回 false。


```
use std::collections::HashMap;

impl Solution {
    /// 判断能否将 nums 分成若干组，每组恰好 k 个互不相同的元素。
    pub fn partition_array(nums: Vec<i32>, k: i32) -> bool {
        let n = nums.len();
        let k = k as usize;

        if n % k != 0 {
            return false;
        }

        let groups = n / k;
        let mut freq = HashMap::new();
        let mut mx = 0usize;

        for &x in &nums {
            let c = freq.entry(x).or_insert(0usize);
            *c += 1;
            mx = mx.max(*c);
        }

        mx <= groups
    }
}
```
