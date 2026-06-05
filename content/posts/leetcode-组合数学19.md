---
title: "leetcode-组合数学19"
date: 2026-05-24T09:54:13+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 美丽子集的数目

给你一个由正整数组成的数组 nums 和一个 正 整数 k 。

如果 nums 的子集中，任意两个整数的绝对差均不等于 k ，则认为该子数组是一个 美丽 子集。

返回数组 nums 中 非空 且 美丽 的子集数目。

nums 的子集定义为：可以经由 nums 删除某些元素（也可能不删除）得到的一个数组。只有在删除元素时选择的索引不同的情况下，两个子集才会被视作是不同的子集。


```
impl Solution {
    pub fn beautiful_subsets(nums: Vec<i32>, k: i32) -> i32 {
        let mut nums = nums;
        nums.sort_unstable(); // 排序使重复元素相邻，方便剪枝（对 k=0 有优化）
        let mut freq = [0; 1001]; // 固定大小数组，比 Vec 更快，无堆分配
        Self::dfs(0, &nums, k, &mut freq) - 1 // 减去空集
    }

    fn dfs(idx: usize, nums: &[i32], k: i32, freq: &mut [i32; 1001]) -> i32 {
        if idx == nums.len() {
            return 1; // 找到一个子集（包括空集）
        }
        // 分支1：不选当前元素
        let mut total = Self::dfs(idx + 1, nums, k, freq);

        let x = nums[idx] as usize;
        let k_usize = k as usize;

        // 检查是否可选当前元素（与已选元素相差不等于 k）
        let can_take = (x < k_usize || freq[x - k_usize] == 0) &&
                       (x + k_usize > 1000 || freq[x + k_usize] == 0);

        if can_take {
            freq[x] += 1;
            total += Self::dfs(idx + 1, nums, k, freq);
            freq[x] -= 1; // 回溯
        }
        total
    }
}
```
