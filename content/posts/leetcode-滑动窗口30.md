---
title: "leetcode-滑动窗口30"
date: 2026-07-18T11:02:31+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 统计「优美子数组」

给你一个整数数组 nums 和一个整数 k。如果某个连续子数组中恰好有 k 个奇数数字，我们就认为这个子数组是「优美子数组」。

请返回这个数组中 「优美子数组」 的数目。


```
impl Solution {
    pub fn number_of_subarrays(nums: Vec<i32>, k: i32) -> i32 {
        let mut count = 0;
        let mut odd_count = 0;
        let mut result = 0;

        // 使用数组代替 HashMap，因为奇数个数范围已知
        // prefix_count[i] 表示前缀和中奇数个数为 i 的出现次数
        let max_odd = nums.len() + 1;
        let mut prefix_count = vec![0; max_odd];
        prefix_count[0] = 1; // 空前缀，奇数个数为0

        for &num in &nums {
            // 当前数字如果是奇数，奇数计数加1
            if num % 2 == 1 {
                odd_count += 1;
            }

            // 如果当前奇数个数 >= k，则存在前缀使得差值为 k
            if odd_count >= k {
                result += prefix_count[(odd_count - k) as usize];
            }

            // 记录当前奇数个数的前缀出现次数
            prefix_count[odd_count as usize] += 1;
        }

        result
    }
}
```
