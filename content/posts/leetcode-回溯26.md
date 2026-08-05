---
title: "leetcode-回溯26"
date: 2026-07-04T10:22:02+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 非递减子序列

给你一个整数数组 nums ，找出并返回所有该数组中不同的递增子序列，递增子序列中 至少有两个元素 。你可以按 任意顺序 返回答案。

数组中可能含有重复元素，如出现两个整数相等，也可以视作递增序列的一种特殊情况。


```
impl Solution {
    /// 找出所有不同的递增子序列（至少两个元素）
    ///
    /// 使用回溯 + 去重策略：
    /// 1. 每个位置可选择"选"或"不选"当前元素
    /// 2. 用集合记录同一层已选过的值，避免重复结果
    /// 3. 维护当前序列最后一个值，保证递增
    pub fn find_subsequences(nums: Vec<i32>) -> Vec<Vec<i32>> {
        let mut ans = Vec::new();
        let mut path = Vec::new();

        fn backtrack(
            nums: &[i32],
            start: usize,
            path: &mut Vec<i32>,
            ans: &mut Vec<Vec<i32>>,
        ) {
            // 找到一个合法递增子序列
            if path.len() >= 2 {
                ans.push(path.clone());
            }

            // 同一层去重：记录本层已经使用过的数字
            use std::collections::HashSet;
            let mut used = HashSet::new();

            for i in start..nums.len() {
                let num = nums[i];

                // 剪枝：如果当前元素小于序列最后一个元素，跳过
                if !path.is_empty() && num < *path.last().unwrap() {
                    continue;
                }

                // 剪枝：同一层已经使用过相同数字，跳过（避免重复结果）
                if used.contains(&num) {
                    continue;
                }

                used.insert(num);
                path.push(num);
                backtrack(nums, i + 1, path, ans);
                path.pop();
            }
        }

        backtrack(&nums, 0, &mut path, &mut ans);
        ans
    }
}
```
