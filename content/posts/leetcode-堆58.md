---
title: "leetcode-堆58"
date: 2026-08-21T09:54:14+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 标记所有下标的最早秒数 II

给你两个下标从 1 开始的整数数组 nums 和 changeIndices ，数组的长度分别为 n 和 m 。

一开始，nums 中所有下标都是未标记的，你的任务是标记 nums 中 所有 下标。

从第 1 秒到第 m 秒（包括 第 m 秒），对于每一秒 s ，你可以执行以下操作 之一 ：

· 选择范围 [1, n] 中的一个下标 i ，并且将 nums[i] 减少 1 。
· 将 nums[changeIndices[s]] 设置成任意的 非负 整数。
· 选择范围 [1, n] 中的一个下标 i ， 满足 nums[i] 等于 0, 并 标记 下标 i 。
· 什么也不做。

请你返回范围 [1, m] 中的一个整数，表示最优操作下，标记 nums 中 所有 下标的 最早秒数 ，如果无法标记所有下标，返回 -1 。


```
use std::cmp::Reverse;
use std::collections::BinaryHeap;

impl Solution {
    pub fn earliest_second_to_mark_indices(nums: Vec<i32>, change_indices: Vec<i32>) -> i32 {
        let n = nums.len();
        let m = change_indices.len();
        let total = n as i64 + nums.iter().map(|&x| x as i64).sum::<i64>();

        // first_t[i] = 下标 i 在 change_indices 中第一次出现的时刻（0-based）
        // 关键：从后往前无条件覆盖，最终得到的是最左边（第一次）出现的位置
        let mut first_t = vec![-1i32; n];
        for t in (0..m).rev() {
            let idx = (change_indices[t] - 1) as usize;
            first_t[idx] = t as i32; // 无条件覆盖！
        }

        // 检查前 mx 秒是否足够标记所有下标
        let check = |mx: usize| -> bool {
            let mut spare = 0i64; // 可留给慢速操作的天数
            let mut slow = total;   // 慢速操作（逐次减1 + 标记）所需天数
            let mut pq = BinaryHeap::new(); // 小根堆：已选"快速清零+标记"的 nums[i]

            for t in (0..mx).rev() {
                let i = (change_indices[t] - 1) as usize;
                let v = nums[i];

                // 不是第一次出现，或 nums[i] <= 1（快速清零不划算）
                if v <= 1 || t as i32 != first_t[i] {
                    spare += 1; // 这一天留给左边做慢速操作
                    continue;
                }

                // 尝试用这一天做"快速清零+标记"（1秒设为0，1秒标记）
                if spare == 0 {
                    // 没有多余天数，考虑反悔之前选的快速清零
                    if let Some(&Reverse(top)) = pq.peek() {
                        if v <= top {
                            spare += 1; // 当前不如之前选的，留给左边
                            continue;
                        }
                        // 反悔：把之前选的快速清零改回慢速
                        slow += top as i64 + 1;
                        pq.pop();
                        spare += 2; // 多出 2 天（快速->慢速）
                    } else {
                        spare += 1;
                        continue;
                    }
                }

                // 选择快速清零+标记
                slow -= v as i64 + 1;
                spare -= 1;
                pq.push(Reverse(v));
            }

            spare >= slow
        };

        // 二分查找最早秒数
        let mut left = n.saturating_sub(1);
        let mut right = m + 1;
        while left + 1 < right {
            let mid = left + (right - left) / 2;
            if check(mid) {
                right = mid;
            } else {
                left = mid;
            }
        }

        if right > m { -1 } else { right as i32 }
    }
}
```
