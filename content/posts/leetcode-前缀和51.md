---
title: "leetcode-前缀和51"
date: 2026-08-25T10:02:49+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 找到数组的中间位置

给你一个下标从 0 开始的整数数组 nums ，请你找到 最左边 的中间位置 middleIndex （也就是所有可能中间位置下标最小的一个）。

中间位置 middleIndex 是满足 nums[0] + nums[1] + ... + nums[middleIndex-1] == nums[middleIndex+1] + nums[middleIndex+2] + ... + nums[nums.length-1] 的数组下标。

如果 middleIndex == 0 ，左边部分的和定义为 0 。类似的，如果 middleIndex == nums.length - 1 ，右边部分的和定义为 0 。

请你返回满足上述条件 最左边 的 middleIndex ，如果不存在这样的中间位置，请你返回 -1 。


```
impl Solution {
    pub fn find_middle_index(nums: Vec<i32>) -> i32 {
        let total: i32 = nums.iter().sum();
        let mut left_sum = 0;

        for (i, &x) in nums.iter().enumerate() {
            // 左边和 == 右边和 等价于 left_sum == total - left_sum - x
            // 即 2 * left_sum == total - x
            if 2 * left_sum == total - x {
                return i as i32;
            }
            left_sum += x;
        }

        -1
    }
}
```
