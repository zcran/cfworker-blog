---
title: "leetcode-计数61"
date: 2026-08-01T10:40:54+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 将数组划分成相等数对

给你一个整数数组 nums ，它包含 2 * n 个整数。

你需要将 nums 划分成 n 个数对，满足：

每个元素 只属于一个 数对。
同一数对中的元素 相等 。

如果可以将 nums 划分成 n 个数对，请你返回 true ，否则返回 false 。


```
impl Solution {
    pub fn divide_array(nums: Vec<i32>) -> bool {
        // 使用 HashSet 记录出现奇数次的数字
        // 每遇到一个数字，如果已存在则移除（配对），否则添加
        let mut odd = std::collections::HashSet::with_capacity(nums.len());

        for num in nums {
            if !odd.insert(num) {
                odd.remove(&num);
            }
        }

        // 所有数字都成对出现时，集合为空
        odd.is_empty()
    }
}
```
