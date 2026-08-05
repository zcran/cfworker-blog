---
title: "leetcode-滑动窗口92"
date: 2026-07-18T11:02:34+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 长度为 K 的子数组的能量值 I

给你一个长度为 n 的整数数组 nums 和一个正整数 k 。

一个数组的 能量值 定义为：

如果 所有 元素都是依次 连续 且 上升 的，那么能量值为 最大 的元素。
否则为 -1 。
你需要求出 nums 中所有长度为 k 的 子数组 的能量值。

请你返回一个长度为 n - k + 1 的整数数组 results ，其中 results[i] 是子数组 nums[i..(i + k - 1)] 的能量值。


```
impl Solution {
    pub fn results_array(nums: Vec<i32>, k: i32) -> Vec<i32> {
        let n = nums.len();
        let k = k as usize;
        let mut ans = vec![-1; n - k + 1];

        // 计算每个位置开始的连续递增序列长度
        let mut consecutive_len = vec![1; n];
        for i in 1..n {
            if nums[i] == nums[i - 1] + 1 {
                consecutive_len[i] = consecutive_len[i - 1] + 1;
            }
        }

        // 检查每个长度为 k 的子数组
        for i in 0..=n - k {
            if consecutive_len[i + k - 1] >= k {
                ans[i] = nums[i + k - 1];
            }
        }

        ans
    }
}
```
