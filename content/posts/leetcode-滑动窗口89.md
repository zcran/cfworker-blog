---
title: "leetcode-滑动窗口89"
date: 2026-07-18T11:02:34+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 找出唯一性数组的中位数

给你一个整数数组 nums 。数组 nums 的 唯一性数组 是一个按元素从小到大排序的数组，包含了 nums 的所有非空 子数组 中不同元素的个数。

换句话说，这是由所有 0 <= i <= j < nums.length 的 distinct(nums[i..j]) 组成的递增数组。

其中，distinct(nums[i..j]) 表示从下标 i 到下标 j 的子数组中不同元素的数量。

返回 nums 唯一性数组 的 中位数 。

注意，数组的 中位数 定义为有序数组的中间元素。如果有两个中间元素，则取值较小的那个。


```
use std::collections::HashMap;

impl Solution {
    pub fn median_of_uniqueness_array(nums: Vec<i32>) -> i32 {
        let n = nums.len() as i64;
        // 唯一性数组的长度：所有非空子数组的数量
        let total_subarrays = n * (n + 1) / 2;
        // 中位数位置（第 k 小，从 1 开始计数）
        let target = (total_subarrays + 1) / 2;

        // 二分查找：找到最小的 t，使得不同元素数 <= t 的子数组数量 >= target
        let mut left = 1;
        let mut right = n as i32;
        let mut ans = 1;

        while left <= right {
            let mid = (left + right) / 2;
            if Self::count_subarrays_with_distinct_leq(&nums, mid as usize) >= target {
                ans = mid;
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }

        ans
    }

    /// 计算不同元素数量 <= limit 的子数组个数
    fn count_subarrays_with_distinct_leq(nums: &[i32], limit: usize) -> i64 {
        let mut freq = HashMap::new();
        let mut left = 0;
        let mut count = 0i64;

        // 滑动窗口：右指针扩展
        for (right, &val) in nums.iter().enumerate() {
            // 1. 添加右端元素
            *freq.entry(val).or_insert(0) += 1;

            // 2. 收缩窗口直到不同元素数 <= limit
            while freq.len() > limit {
                let left_val = nums[left];
                if let Some(c) = freq.get_mut(&left_val) {
                    *c -= 1;
                    if *c == 0 {
                        freq.remove(&left_val);
                    }
                }
                left += 1;
            }

            // 3. 以 right 结尾的所有子数组 [left..=right], [left+1..=right], ..., [right..=right]
            // 都满足不同元素数 <= limit，共 (right - left + 1) 个
            count += (right - left + 1) as i64;
        }

        count
    }
}
```
