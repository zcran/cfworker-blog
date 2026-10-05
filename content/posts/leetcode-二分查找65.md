---
title: "leetcode-二分查找65"
date: 2026-09-28T12:29:14+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 最小化数对的最大差值

给你一个下标从 0 开始的整数数组 nums 和一个整数 p 。请你从 nums 中找到 p 个下标对，每个下标对对应数值取差值，你需要使得这 p 个差值的 最大值 最小。同时，你需要确保每个下标在这 p 个下标对中最多出现一次。

对于一个下标对 i 和 j ，这一对的差值为 |nums[i] - nums[j]| ，其中 |x| 表示 x 的 绝对值 。

请你返回 p 个下标对对应数值 最大差值 的 最小值 。我们定义空集的最大值为零。


```
impl Solution {
    pub fn minimize_max(mut nums: Vec<i32>, p: i32) -> i32 {
        nums.sort_unstable();

        // 判断最大差值不超过 mx 时，能否选出至少 p 对不相交下标对
        let can_pick = |mx: i32| -> bool {
            let mut cnt = 0;
            let mut i = 0;
            while i + 1 < nums.len() {
                if nums[i + 1] - nums[i] <= mx {
                    cnt += 1;
                    i += 2; // 相邻两数配对，跳过下一间
                } else {
                    i += 1;
                }
            }
            cnt >= p
        };

        let mut left = 0;
        let mut right = nums[nums.len() - 1] - nums[0];
        while left < right {
            let mid = left + (right - left) / 2;
            if can_pick(mid) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        left
    }
}
```
