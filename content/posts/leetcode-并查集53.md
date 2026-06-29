---
title: "leetcode-并查集53"
date: 2026-06-25T11:22:23+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 交换得到字典序最小的数组


给你一个下标从 0 开始的 正整数 数组 nums 和一个 正整数 limit 。

在一次操作中，你可以选择任意两个下标 i 和 j，如果 满足 |nums[i] - nums[j]| <= limit ，则交换 nums[i] 和 nums[j] 。

返回执行任意次操作后能得到的 字典序最小的数组 。

如果在数组 a 和数组 b 第一个不同的位置上，数组 a 中的对应元素比数组 b 中的对应元素的字典序更小，则认为数组 a 就比数组 b 字典序更小。例如，数组 [2,10,3] 比数组 [10,2,3] 字典序更小，下标 0 处是两个数组第一个不同的位置，且 2 < 10 。



```
impl Solution {
    pub fn lexicographically_smallest_array(nums: Vec<i32>, limit: i32) -> Vec<i32> {
        let n = nums.len();

        // 按值排序索引
        let mut indices: Vec<usize> = (0..n).collect();
        indices.sort_unstable_by_key(|&i| nums[i]);

        let mut result = vec![0; n];
        let mut group_start = 0;
        let mut group_values = Vec::new();

        // 遍历排序后的索引，将相邻差值 <= limit 的元素分为一组
        for i in 0..n {
            let cur_val = nums[indices[i]];

            // 检测是否开始新组（当前值与上一值差值 > limit）
            if i > 0 && cur_val - nums[indices[i - 1]] > limit {
                // 处理旧组：将组内索引升序排列，并将组内值（已升序）依次赋给这些索引
                let mut group_indices = &mut indices[group_start..i];
                group_indices.sort_unstable();
                for (&idx, &val) in group_indices.iter().zip(group_values.iter()) {
                    result[idx] = val;
                }

                // 重置新组
                group_start = i;
                group_values.clear();
            }

            group_values.push(cur_val);
        }

        // 处理最后一组
        if group_start < n {
            let mut group_indices = &mut indices[group_start..n];
            group_indices.sort_unstable();
            for (&idx, &val) in group_indices.iter().zip(group_values.iter()) {
                result[idx] = val;
            }
        }

        result
    }
}
```
