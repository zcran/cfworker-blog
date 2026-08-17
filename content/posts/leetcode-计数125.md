---
title: "leetcode-计数125"
date: 2026-08-01T10:40:57+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 不同频率的最小数对

给你一个整数数组 nums。

从 nums 中找出两个 互不相同 的值 x 和 y，使得：

· x < y
· x 和 y 在 nums 中的频率不同。

在所有满足条件的数对中：

· 选择 x 的值尽可能小的数对。
· 如果存在多个 x 相同的数对，选择 y 的值尽可能小的那个。

返回一个整数数组 [x, y]。如果不存在有效的数对，返回 [-1, -1]。

一个值 x 的 频率 是指它在数组中出现的次数。


```
impl Solution {
    /// 找出满足条件的最小数对 [x, y]：
    /// - x < y，且 x、y 频率不同
    /// - x 尽可能小，x 相同时 y 尽可能小
    pub fn min_distinct_freq_pair(nums: Vec<i32>) -> Vec<i32> {
        use std::collections::HashMap;

        // 统计频率，同时找出最小值 mn
        let mut freq = HashMap::with_capacity(nums.len());
        let mut mn = i32::MAX;
        for &x in &nums {
            *freq.entry(x).or_insert(0) += 1;
            if x < mn {
                mn = x;
            }
        }

        // 最小值的频率
        let cnt_mn = freq[&mn];

        // 找频率与 mn 不同、且值最小的 y（y 自动 > mn，因为 map 键唯一）
        let mut min_y = i32::MAX;
        for (&y, &c) in &freq {
            if c != cnt_mn && y < min_y {
                min_y = y;
            }
        }

        if min_y == i32::MAX {
            vec![-1, -1]
        } else {
            vec![mn, min_y]
        }
    }
}
```
