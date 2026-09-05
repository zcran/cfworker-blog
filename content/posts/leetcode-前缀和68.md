---
title: "leetcode-前缀和68"
date: 2026-08-25T10:02:50+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 找到所有好下标

给你一个大小为 n 下标从 0 开始的整数数组 nums 和一个正整数 k 。

对于 k <= i < n - k 之间的一个下标 i ，如果它满足以下条件，我们就称它为一个 好 下标：

下标 i 之前 的 k 个元素是 非递增的 。
下标 i 之后 的 k 个元素是 非递减的 。

按 升序 返回所有好下标。


```
impl Solution {
    pub fn good_indices(nums: Vec<i32>, k: i32) -> Vec<i32> {
        let n = nums.len();
        let k = k as usize;

        // 如果 k <= 1，所有中间位置都是好下标
        if k <= 1 {
            return (1..n as i32 - 1).collect();
        }

        // non_increasing[i] = 以 nums[i] 结尾的连续非递减段长度
        // 实际记录的是"向左连续非递增"的长度
        let mut non_inc = vec![1; n];
        for i in 1..n {
            if nums[i] <= nums[i - 1] {
                non_inc[i] = non_inc[i - 1] + 1;
            }
        }

        // non_decreasing[i] = 以 nums[i] 开始的连续非递减段长度
        // 实际记录的是"向右连续非递减"的长度
        let mut non_dec = vec![1; n];
        for i in (0..n - 1).rev() {
            if nums[i] <= nums[i + 1] {
                non_dec[i] = non_dec[i + 1] + 1;
            }
        }

        // 收集好下标：i 左边至少有 k 个非递增，右边至少有 k 个非递减
        let mut ans = Vec::new();
        for i in k..n - k {
            if non_inc[i - 1] >= k && non_dec[i + 1] >= k {
                ans.push(i as i32);
            }
        }

        ans
    }
}
```
