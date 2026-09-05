---
title: "leetcode-前缀和35"
date: 2026-08-25T10:02:48+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 有序数组中差绝对值之和

给你一个 非递减 有序整数数组 nums 。

请你建立并返回一个整数数组 result，它跟 nums 长度相同，且result[i] 等于 nums[i] 与数组中所有其他元素差的绝对值之和。

换句话说， result[i] 等于 sum(|nums[i]-nums[j]|) ，其中 0 <= j < nums.length 且 j != i （下标从 0 开始）。


```
impl Solution {
    /// 计算每个元素与数组中所有其他元素差的绝对值之和
    ///
    /// 由于数组非递减，对于 nums[i]：
    /// - 左侧元素都 <= nums[i]，贡献为 nums[i]*i - prefix[i]
    /// - 右侧元素都 >= nums[i]，贡献为 (total - prefix[i+1]) - nums[i]*(n-i-1)
    pub fn get_sum_absolute_differences(nums: Vec<i32>) -> Vec<i32> {
        let n = nums.len();
        let total: i32 = nums.iter().sum();
        let mut prefix = 0;
        let mut ans = vec![0; n];

        for (i, &x) in nums.iter().enumerate() {
            // 左侧贡献：x * i - prefix
            // 右侧贡献：(total - prefix - x) - x * (n - i - 1)
            // 合并后更简洁的形式
            ans[i] = x * i as i32 - prefix + (total - prefix - x) - x * (n - i - 1) as i32;
            prefix += x;
        }

        ans
    }
}
```
