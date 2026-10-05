---
title: "leetcode-双指针117"
date: 2026-09-05T09:53:08+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 子序列首尾元素的最大乘积

给你一个整数数组 nums 和一个整数 m。

返回任意大小为 m 的 子序列 中首尾元素乘积的最大值。

子序列 是可以通过删除原数组中的一些元素（或不删除任何元素），且不改变剩余元素顺序而得到的数组。


```
impl Solution {
    /// 返回任意大小为 m 的子序列中首尾元素乘积的最大值。
    ///
    /// 思路：固定子序列的最后一个元素 nums[i]（i >= m-1），
    /// 则首元素可以是 nums[0..=i-m+1] 中任意一个。
    /// 维护该前缀范围内的最小值和最大值，用 nums[i] 分别与它们相乘取最大即可。
    /// 最小值用于处理负负得正的情况。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn maximum_product(nums: Vec<i32>, m: i32) -> i64 {
        let m = m as usize;
        let mut ans = i64::MIN;
        let mut mn = i32::MAX;
        let mut mx = i32::MIN;

        for i in m - 1..nums.len() {
            // 将新进入候选首元素范围的 nums[i - m + 1] 纳入最小/最大值
            let y = nums[i - m + 1];
            mn = mn.min(y);
            mx = mx.max(y);

            // 以 nums[i] 作为子序列末尾，与候选首元素相乘
            let x = nums[i] as i64;
            ans = ans.max(x * mn as i64).max(x * mx as i64);
        }

        ans
    }
}
```
