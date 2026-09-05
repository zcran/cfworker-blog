---
title: "leetcode-前缀和120"
date: 2026-08-25T10:02:53+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 分割数组得到最小绝对差

给你一个整数数组 nums。

将数组 恰好 分成两个子数组 left 和 right ，使得 left 严格递增 ，right 严格递减 。

返回 left 与 right 的元素和之间 绝对差值的最小可能值 。如果不存在有效的分割方案，则返回 -1 。

子数组 是数组中连续的非空元素序列。

当数组中每个元素都严格大于其前一个元素（如果存在）时，称该数组为严格递增。

当数组中每个元素都严格小于其前一个元素（如果存在）时，称该数组为严格递减。


```
impl Solution {
    pub fn split_array(nums: Vec<i32>) -> i64 {
        let n = nums.len();
        if n < 2 {
            return -1;
        }

        // 前缀和
        let mut prefix = vec![0i64; n + 1];
        for i in 0..n {
            prefix[i + 1] = prefix[i] + nums[i] as i64;
        }

        // inc[i] 表示 nums[0..=i] 是否严格递增
        let mut inc = vec![false; n];
        inc[0] = true;
        for i in 1..n {
            inc[i] = inc[i - 1] && nums[i] > nums[i - 1];
        }

        // dec[i] 表示 nums[i..=n-1] 是否严格递减
        let mut dec = vec![false; n];
        dec[n - 1] = true;
        for i in (0..n - 1).rev() {
            dec[i] = dec[i + 1] && nums[i] > nums[i + 1];
        }

        let mut ans = i64::MAX;
        // 分割点 p 将数组分为 [0..p-1] 和 [p..n-1]
        for p in 1..n {
            if inc[p - 1] && dec[p] {
                let left_sum = prefix[p];
                let right_sum = prefix[n] - prefix[p];
                let diff = (left_sum - right_sum).abs();
                if diff < ans {
                    ans = diff;
                }
            }
        }

        if ans == i64::MAX { -1 } else { ans }
    }
}
```
