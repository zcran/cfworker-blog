---
title: "leetcode-滑动窗口79"
date: 2026-07-18T11:02:33+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 统计最大元素出现至少 K 次的子数组

给你一个整数数组 nums 和一个 正整数 k 。

请你统计有多少满足 「 nums 中的 最大 元素」至少出现 k 次的子数组，并返回满足这一条件的子数组的数目。

子数组是数组中的一个连续元素序列。


```
impl Solution {
    pub fn count_subarrays(nums: Vec<i32>, k: i32) -> i64 {
        let n = nums.len();
        // 找到数组中的最大值
        let max_val = *nums.iter().max().unwrap();

        // 记录最大值出现的位置，添加哨兵 -1 便于计算
        let mut max_pos = vec![-1];
        for (i, &num) in nums.iter().enumerate() {
            if num == max_val {
                max_pos.push(i as i32);
            }
        }

        // 如果最大值出现的次数少于 k，不可能有满足条件的子数组
        if max_pos.len() - 1 < k as usize {
            return 0;
        }

        let mut left = 1;       // 当前窗口第一个最大值的索引（在 max_pos 中）
        let mut right = k as usize; // 当前窗口第 k 个最大值的索引（在 max_pos 中）
        let mut ans = 0i64;

        // 滑动窗口，窗口内恰好包含 k 个最大值
        while right < max_pos.len() {
            // left 和 right 是 max_pos 中的索引
            // max_pos[left] 是第 left 个最大值的位置
            // max_pos[right] 是第 right 个最大值的位置

            // 左边界可选范围：从上一个最大值之后到当前第一个最大值
            // (max_pos[left] - max_pos[left-1]) 表示有多少个起始位置
            let left_choices = (max_pos[left] - max_pos[left - 1]) as i64;

            // 右边界可选范围：从当前第 k 个最大值到数组末尾（或下一个最大值之前）
            // (n as i32 - max_pos[right]) 表示有多少个结束位置
            let right_choices = (n as i32 - max_pos[right]) as i64;

            ans += left_choices * right_choices;

            left += 1;
            right += 1;
        }

        ans
    }
}
```
