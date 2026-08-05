---
title: "leetcode-回溯54"
date: 2026-07-04T10:22:04+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 统计按位或能得到最大值的子集数目

给你一个整数数组 nums ，请你找出 nums 子集 按位或 可能得到的 最大值 ，并返回按位或能得到最大值的 不同非空子集的数目 。

如果数组 a 可以由数组 b 删除一些元素（或不删除）得到，则认为数组 a 是数组 b 的一个 子集 。如果选中的元素下标位置不一样，则认为两个子集 不同 。

对数组 a 执行 按位或 ，结果等于 a[0] OR a[1] OR ... OR a[a.length - 1]（下标从 0 开始）。




```
impl Solution {
    pub fn count_max_or_subsets(nums: Vec<i32>) -> i32 {
        // 计算全局最大值
        let max_or = nums.iter().fold(0, |acc, &x| acc | x);
        // 从索引0开始，初始值为0
        Self::dfs(&nums, 0, 0, max_or)
    }

    fn dfs(nums: &[i32], start: usize, curr_or: i32, max_or: i32) -> i32 {
        let mut count = 0;

        for i in start..nums.len() {
            let new_or = curr_or | nums[i];

            // 如果当前子集已经达到最大值，计数+1
            if new_or == max_or {
                count += 1;
            }

            // 继续递归添加后续元素（即使已经达到最大值也要继续）
            // 因为添加更多元素后OR值不会改变，仍然等于最大值
            count += Self::dfs(nums, i + 1, new_or, max_or);
        }

        count
    }
}
```
