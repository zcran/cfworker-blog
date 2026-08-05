---
title: "leetcode-计数56"
date: 2026-08-01T10:40:53+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 找出数组中的所有孤独数字

给你一个整数数组 nums 。如果数字 x 在数组中仅出现 一次 ，且没有 相邻 数字（即，x + 1 和 x - 1）出现在数组中，则认为数字 x 是 孤独数字 。

返回 nums 中的 所有 孤独数字。你可以按 任何顺序 返回答案。


```
use std::collections::HashSet;

impl Solution {
    pub fn find_lonely(nums: Vec<i32>) -> Vec<i32> {
        // 使用 HashSet 存储所有数字，用于 O(1) 查找相邻数字
        let set: HashSet<i32> = nums.iter().copied().collect();

        // 使用数组统计每个数字出现次数（原地修改，节省内存）
        let mut nums = nums;
        nums.sort_unstable();

        let mut ans = Vec::new();
        let mut i = 0;
        while i < nums.len() {
            let cur = nums[i];
            let mut count = 1;
            // 统计当前数字出现次数
            while i + 1 < nums.len() && nums[i + 1] == cur {
                count += 1;
                i += 1;
            }

            // 出现一次且没有相邻数字
            if count == 1 && !set.contains(&(cur - 1)) && !set.contains(&(cur + 1)) {
                ans.push(cur);
            }
            i += 1;
        }

        ans
    }
}
```
