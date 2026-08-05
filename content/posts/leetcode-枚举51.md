---
title: "leetcode-枚举51"
date: 2026-07-09T10:05:01+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 统计移除递增子数组的数目 I

给你一个下标从 0 开始的 正 整数数组 nums 。

如果 nums 的一个子数组满足：移除这个子数组后剩余元素 严格递增 ，那么我们称这个子数组为 移除递增 子数组。比方说，[5, 3, 4, 6, 7] 中的 [3, 4] 是一个移除递增子数组，因为移除该子数组后，[5, 3, 4, 6, 7] 变为 [5, 6, 7] ，是严格递增的。

请你返回 nums 中 移除递增 子数组的总数目。

注意 ，剩余元素为空的数组也视为是递增的。

子数组 指的是一个数组中一段非空且连续的元素序列。


```
impl Solution {
    pub fn incremovable_subarray_count(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        if n <= 1 {
            return n as i32; // 空数组或单元素数组，所有子数组都满足条件
        }

        // 计算前缀最长严格递增序列长度
        // pref[i] 表示从 0 到 i 是否严格递增
        let mut pref = vec![true; n];
        for i in 1..n {
            if nums[i] <= nums[i - 1] {
                pref[i] = false;
            } else {
                pref[i] = pref[i - 1];
            }
        }

        // 计算后缀最长严格递增序列长度
        // suff[i] 表示从 i 到 n-1 是否严格递增
        let mut suff = vec![true; n];
        for i in (0..n - 1).rev() {
            if nums[i] >= nums[i + 1] {
                suff[i] = false;
            } else {
                suff[i] = suff[i + 1];
            }
        }

        let mut ans = 0;

        // 枚举移除子数组的左右边界 [l, r]
        for l in 0..n {
            for r in l..n {
                // 条件1：移除前部分 [0, l-1] 必须严格递增
                if l > 0 && !pref[l - 1] {
                    continue;
                }
                // 条件2：移除后部分 [r+1, n-1] 必须严格递增
                if r + 1 < n && !suff[r + 1] {
                    continue;
                }
                // 条件3：前部分最后一个元素 < 后部分第一个元素
                if l > 0 && r + 1 < n && nums[l - 1] >= nums[r + 1] {
                    continue;
                }
                ans += 1;
            }
        }

        ans
    }
}
```
