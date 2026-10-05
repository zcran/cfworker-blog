---
title: "leetcode-二分查找71"
date: 2026-09-28T12:29:15+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 标记所有下标的最早秒数 I

给你两个下标从 1 开始的整数数组 nums 和 changeIndices ，数组的长度分别为 n 和 m 。

一开始，nums 中所有下标都是未标记的，你的任务是标记 nums 中 所有 下标。

从第 1 秒到第 m 秒（包括 第 m 秒），对于每一秒 s ，你可以执行以下操作 之一 ：

· 选择范围 [1, n] 中的一个下标 i ，并且将 nums[i] 减少 1 。
· 如果 nums[changeIndices[s]] 等于 0 ，标记 下标 changeIndices[s] 。
· 什么也不做。

请你返回范围 [1, m] 中的一个整数，表示最优操作下，标记 nums 中 所有 下标的 最早秒数 ，如果无法标记所有下标，返回 -1 。


```
impl Solution {
    pub fn earliest_second_to_mark_indices(nums: Vec<i32>, change_indices: Vec<i32>) -> i32 {
        let n = nums.len();
        let m = change_indices.len();
        // 转为 0-based 下标
        let change: Vec<usize> = change_indices.iter().map(|&x| x as usize - 1).collect();

        // 判断在前 t 秒内能否标记所有下标
        let can_mark = |t: usize| -> bool {
            // last[i] 记录下标 i 在 [0, t) 内最后一次出现的秒数
            let mut last = vec![usize::MAX; n];
            for s in 0..t {
                last[change[s]] = s;
            }

            // 收集所有需要标记的下标及其最晚标记时间，按时间排序
            let mut events: Vec<(usize, i32)> = Vec::with_capacity(n);
            for i in 0..n {
                if last[i] == usize::MAX {
                    return false; // 该下标从未出现，无法标记
                }
                events.push((last[i], nums[i] + 1)); // +1 表示标记本身也占一秒
            }
            events.sort_unstable();

            // 贪心：按最晚时间依次安排，累计耗时不能超过当前秒数
            let mut used = 0_i64;
            for (time, need) in events {
                used += need as i64;
                if used > time as i64 + 1 {
                    return false;
                }
            }
            true
        };

        // 二分最早秒数
        let mut left = 1;
        let mut right = m + 1;
        while left < right {
            let mid = left + (right - left) / 2;
            if can_mark(mid) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }

        if left <= m { left as i32 } else { -1 }
    }
}
```
