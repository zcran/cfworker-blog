---
title: "leetcode-回溯14"
date: 2026-07-04T10:22:02+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 子集 II

给你一个整数数组 nums ，其中可能包含重复元素，请你返回该数组所有可能的 子集（幂集）。

解集 不能 包含重复的子集。返回的解集中，子集可以按 任意顺序 排列。


```
impl Solution {
    pub fn subsets_with_dup(mut nums: Vec<i32>) -> Vec<Vec<i32>> {
        nums.sort();
        let mut result = Vec::with_capacity(1 << nums.len());
        let mut current = Vec::new();
        Self::backtrack(&nums, 0, &mut current, &mut result);
        result
    }

    /// 回溯生成所有不重复子集
    /// - nums: 已排序的数组
    /// - start: 当前搜索的起始位置
    /// - current: 当前正在构建的子集
    /// - result: 存储所有子集
    fn backtrack(nums: &[i32], start: usize, current: &mut Vec<i32>, result: &mut Vec<Vec<i32>>) {
        // 每个状态都是一个有效子集
        result.push(current.clone());

        // 从 start 开始遍历，避免重复
        for i in start..nums.len() {
            // 跳过重复元素：如果当前元素和前一个相同，且前一个未被选择
            // 这保证了相同元素的组合只会在第一次出现时被生成
            if i > start && nums[i] == nums[i - 1] {
                continue;
            }

            current.push(nums[i]);
            Self::backtrack(nums, i + 1, current, result);
            current.pop();
        }
    }
}
```
