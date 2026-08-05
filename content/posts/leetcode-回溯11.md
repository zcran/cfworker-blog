---
title: "leetcode-回溯11"
date: 2026-07-04T10:22:02+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 子集

给你一个整数数组 nums ，数组中的元素 互不相同 。返回该数组所有可能的子集（幂集）。

解集 不能 包含重复的子集。你可以按 任意顺序 返回解集。


```
impl Solution {
    pub fn subsets(nums: Vec<i32>) -> Vec<Vec<i32>> {
        let mut result = Vec::with_capacity(1 << nums.len());
        let mut current = Vec::with_capacity(nums.len());

        Self::backtrack(0, &nums, &mut current, &mut result);
        result
    }

    /// 回溯生成所有子集
    /// - start: 当前处理的位置
    /// - nums: 原始数组
    /// - current: 当前正在构建的子集
    /// - result: 存储所有子集
    fn backtrack(start: usize, nums: &[i32], current: &mut Vec<i32>, result: &mut Vec<Vec<i32>>) {
        // 每个状态都是一个有效子集，直接加入结果
        result.push(current.clone());

        // 从 start 开始遍历，确保不重复
        for i in start..nums.len() {
            current.push(nums[i]);          // 选择当前元素
            Self::backtrack(i + 1, nums, current, result);
            current.pop();                  // 回溯
        }
    }
}
```
