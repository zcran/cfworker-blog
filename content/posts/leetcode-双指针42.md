---
title: "leetcode-双指针42"
date: 2026-09-05T09:53:03+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 找出第 K 小的数对距离

数对 (a,b) 由整数 a 和 b 组成，其数对距离定义为 a 和 b 的绝对差值。

给你一个整数数组 nums 和一个整数 k ，数对由 nums[i] 和 nums[j] 组成且满足 0 <= i < j < nums.length 。返回 所有数对距离中 第 k 小的数对距离。


```
impl Solution {
    pub fn smallest_distance_pair(mut nums: Vec<i32>, k: i32) -> i32 {
        nums.sort_unstable();
        let n = nums.len();

        // 二分查找：距离范围在 [0, max - min] 之间
        let (mut left, mut right) = (0, nums[n - 1] - nums[0]);

        while left < right {
            let mid = left + (right - left) / 2;

            // 统计距离 <= mid 的数对数量
            let mut count = 0;
            let mut j = 0;
            for i in 0..n {
                // 移动右指针，使得 nums[j] - nums[i] <= mid
                while j < n && nums[j] - nums[i] <= mid {
                    j += 1;
                }
                // 对于当前 i，j 之前的元素（除自身外）都满足条件
                count += (j - i - 1) as i32;
            }

            // 如果数量 >= k，说明答案在左半部分（含 mid）
            if count >= k {
                right = mid;
            } else {
                left = mid + 1;
            }
        }

        left
    }
}
```
