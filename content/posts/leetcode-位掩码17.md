---
title: "leetcode-位掩码17"
date: 2026-05-18T10:27:59+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 最接近目标值的子序列和

给你一个整数数组 nums 和一个目标值 goal 。

你需要从 nums 中选出一个子序列，使子序列元素总和最接近 goal 。也就是说，如果子序列元素和为 sum ，你需要 最小化绝对差 abs(sum - goal) 。

返回 abs(sum - goal) 可能的 最小值 。

注意，数组的子序列是通过移除原始数组中的某些元素（可能全部或无）而形成的数组。

```
impl Solution {
    pub fn min_abs_difference(nums: Vec<i32>, goal: i32) -> i32 {
        let n = nums.len();
        let left_len = n / 2;
        let right_len = n - left_len;

        // 生成子集和：使用 fold 构建所有子集和（函数式）
        let left_sums = (0..(1 << left_len))
            .map(|mask| {
                (0..left_len)
                    .filter(|&i| (mask >> i) & 1 == 1)
                    .map(|i| nums[i])
                    .sum()
            })
            .collect::<Vec<i32>>();

        let right_sums = (0..(1 << right_len))
            .map(|mask| {
                (0..right_len)
                    .filter(|&i| (mask >> i) & 1 == 1)
                    .map(|i| nums[left_len + i])
                    .sum()
            })
            .collect::<Vec<i32>>();

        // 排序去重（函数式链）
        let mut left = left_sums;
        left.sort_unstable();
        left.dedup();
        let mut right = right_sums;
        right.sort_unstable();
        right.dedup();

        // 函数式查找最小差值
        left.iter()
            .map(|&l| goal - l)
            .flat_map(|target| {
                let idx = right.partition_point(|&x| x < target);
                let mut diffs = vec![];
                if idx < right.len() {
                    diffs.push((target - right[idx]).abs());
                }
                if idx > 0 {
                    diffs.push((target - right[idx - 1]).abs());
                }
                diffs
            })
            .min()
            .unwrap()
    }
}
```
