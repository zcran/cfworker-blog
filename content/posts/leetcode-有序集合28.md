---
title: "leetcode-有序集合28"
date: 2026-06-14T10:47:42+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 限制条件下元素之间的最小绝对差

给你一个下标从 0 开始的整数数组 nums 和一个整数 x 。

请你找到数组中下标距离至少为 x 的两个元素的 差值绝对值 的 最小值 。

换言之，请你找到两个下标 i 和 j ，满足 abs(i - j) >= x 且 abs(nums[i] - nums[j]) 的值最小。

请你返回一个整数，表示下标距离至少为 x 的两个元素之间的差值绝对值的 最小值 。


```
use std::collections::BTreeSet;

impl Solution {
    pub fn min_absolute_difference(nums: Vec<i32>, x: i32) -> i32 {
        let x = x as usize;
        let mut ans = i32::MAX;
        let mut set = BTreeSet::new();

        // 维护一个窗口 [0, i-x] 内的元素集合
        for i in x..nums.len() {
            // 将距离当前位置至少 x 的元素加入集合
            set.insert(nums[i - x]);

            // 在当前元素附近查找最接近的值
            let y = nums[i];
            if let Some(&next) = set.range(y..).next() {
                ans = ans.min(next - y);
            }
            if let Some(&prev) = set.range(..=y).next_back() {
                ans = ans.min(y - prev);
            }

            // 如果已经达到最小值，提前返回
            if ans == 0 {
                return 0;
            }
        }

        ans
    }
}
```
