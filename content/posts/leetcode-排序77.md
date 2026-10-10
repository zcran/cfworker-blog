---
title: "leetcode-排序77"
date: 2026-10-05T00:35:05+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 找出缺失的元素

给你一个整数数组 nums ，数组由若干 互不相同 的整数组成。

数组 nums 原本包含了某个范围内的 所有整数 。但现在，其中可能 缺失 部分整数。

该范围内的 最小 整数和 最大 整数仍然存在于 nums 中。

返回一个 有序 列表，包含该范围内缺失的所有整数，并 按从小到大排序。如果没有缺失的整数，返回一个 空 列表。


```
impl Solution {
    pub fn find_missing_elements(mut nums: Vec<i32>) -> Vec<i32> {
        // 排序后，相邻元素之间的空隙即为缺失的整数
        nums.sort_unstable();

        let mut missing = Vec::new();
        for w in nums.windows(2) {
            // 将 (prev, cur) 之间的所有整数加入结果
            missing.extend(w[0] + 1..w[1]);
        }

        missing
    }
}
```
