---
title: "leetcode-枚举38"
date: 2026-07-09T10:05:01+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 从两个数字数组里生成最小数字

给你两个只包含 1 到 9 之间数字的数组 nums1 和 nums2 ，每个数组中的元素 互不相同 ，请你返回 最小 的数字，两个数组都 至少 包含这个数字的某个数位。


```
impl Solution {
    pub fn min_number(nums1: Vec<i32>, nums2: Vec<i32>) -> i32 {
        // 使用位掩码记录每个数组包含的数字
        let mut mask1 = 0u16;
        let mut mask2 = 0u16;
        let mut min1 = 10;
        let mut min2 = 10;

        for &num in &nums1 {
            let bit = 1 << num;
            mask1 |= bit;
            min1 = min1.min(num);
        }

        for &num in &nums2 {
            let bit = 1 << num;
            mask2 |= bit;
            min2 = min2.min(num);
        }

        // 查找共同的数字（从1到9）
        let common = mask1 & mask2;
        if common != 0 {
            // 返回最小的共同数字
            return common.trailing_zeros() as i32;
        }

        // 没有共同数字：取两个最小值组成两位数
        // 较小的数字放在十位，较大的放在个位，以得到最小数
        if min1 < min2 {
            min1 * 10 + min2
        } else {
            min2 * 10 + min1
        }
    }
}
```
