---
title: "leetcode-前缀和29"
date: 2026-08-25T10:02:47+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 和为目标值且不重叠的非空子数组的最大数目

给你一个数组 nums 和一个整数 target 。

请你返回 非空不重叠 子数组的最大数目，且每个子数组中数字和都为 target 。




```
impl Solution {
    /// 返回非空不重叠子数组的最大数目，每个子数组和均为 target
    /// 使用前缀和 + 贪心：每次找到最早结束的合法区间
    pub fn max_non_overlapping(nums: Vec<i32>, target: i32) -> i32 {
        let mut ans = 0;
        let mut last_end = 0;          // 上一个选中区间的结束位置（前缀和索引）
        let mut prefix = 0;            // 当前前缀和
        let mut seen = std::collections::HashMap::new();
        seen.insert(0, 0);             // 前缀和 0 出现在索引 0

        for i in 1..=nums.len() {
            prefix += nums[i - 1];
            let need = prefix - target;

            // 若存在前缀和 need，且其位置 >= last_end，则找到一个不重叠区间
            if let Some(&start) = seen.get(&need) {
                if start >= last_end {
                    ans += 1;
                    last_end = i;      // 更新为当前区间结束位置
                }
            }

            // 只保留最新出现的相同前缀和（贪心：越靠右越有利于后续不重叠）
            seen.insert(prefix, i);
        }

        ans
    }
}
```
