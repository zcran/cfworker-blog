---
title: "leetcode-前缀和109"
date: 2026-08-25T10:02:52+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 长度可被 K 整除的子数组的最大元素和

给你一个整数数组 nums 和一个整数 k 。

返回 nums 中一个 非空子数组 的 最大 和，要求该子数组的长度可以 被 k 整除。


```
impl Solution {
    pub fn max_subarray_sum(nums: Vec<i32>, k: i32) -> i64 {
        let k = k as usize;
        // 存储每个余数类的最小前缀和，初始为最大值
        let mut min_prefix = vec![i64::MAX; k];
        min_prefix[k - 1] = 0; // 空前缀（长度为0）的余数为k-1

        let mut prefix_sum = 0i64;
        let mut max_sum = i64::MIN;

        for (i, &num) in nums.iter().enumerate() {
            prefix_sum += num as i64;
            let rem = i % k;

            // 当前前缀和减去同余类的最小前缀和，即为可被k整除的子数组和
            if min_prefix[rem] != i64::MAX {
                max_sum = max_sum.max(prefix_sum - min_prefix[rem]);
            }

            // 更新当前余数类的最小前缀和
            if prefix_sum < min_prefix[rem] {
                min_prefix[rem] = prefix_sum;
            }
        }

        max_sum
    }
}
```
