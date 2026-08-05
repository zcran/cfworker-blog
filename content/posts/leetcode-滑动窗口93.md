---
title: "leetcode-滑动窗口93"
date: 2026-07-18T11:02:34+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 长度为 K 的子数组的能量值 II

给你一个长度为 n 的整数数组 nums 和一个正整数 k 。

一个数组的 能量值 定义为：

如果 所有 元素都是依次 连续（即 nums[i] + 1 = nums[i + 1]，i < n）且 上升 的，那么能量值为 最大 的元素。
否则为 -1 。
你需要求出 nums 中所有长度为 k 的 子数组 的能量值。

请你返回一个长度为 n - k + 1 的整数数组 results ，其中 results[i] 是子数组 nums[i..(i + k - 1)] 的能量值。


```
impl Solution {
    pub fn results_array(nums: Vec<i32>, k: i32) -> Vec<i32> {
        let n = nums.len();
        let k = k as usize;
        let mut ans = vec![-1; n - k + 1];

        // 记录以当前位置结尾的连续递增序列长度
        let mut consecutive_len = 1;

        for i in 0..n {
            // 更新连续递增序列长度
            if i > 0 && nums[i] == nums[i - 1] + 1 {
                consecutive_len += 1;
            } else {
                consecutive_len = 1;
            }

            // 当连续递增长度达到 k 时，以 i 结尾的长度为 k 的子数组有效
            if consecutive_len >= k {
                ans[i - k + 1] = nums[i];
            }
        }

        ans
    }
}
```
