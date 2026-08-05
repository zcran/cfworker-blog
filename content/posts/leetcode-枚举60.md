---
title: "leetcode-枚举60"
date: 2026-07-09T10:05:02+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 找出与数组相加的整数 II

给你两个整数数组 nums1 和 nums2。

从 nums1 中移除两个元素，并且所有其他元素都与变量 x 所表示的整数相加。如果 x 为负数，则表现为元素值的减少。

执行上述操作后，nums1 和 nums2 相等 。当两个数组中包含相同的整数，并且这些整数出现的频次相同时，两个数组 相等 。

返回能够实现数组相等的 最小 整数 x 。


```
impl Solution {
    pub fn minimum_added_integer(nums1: Vec<i32>, nums2: Vec<i32>) -> i32 {
        let mut nums1 = nums1;
        let mut nums2 = nums2;
        nums1.sort_unstable();
        nums2.sort_unstable();

        let mut ans = i32::MAX;

        // 枚举 nums1 中第一个与 nums2[0] 匹配的元素位置（只能在前 3 个中）
        for start in 0..=2 {
            let diff = nums2[0] - nums1[start];
            let mut j = 0;          // nums2 的指针
            let mut skipped = 0;    // 在 start 之后跳过的元素数（即被移除的）

            // 从 start 开始扫描 nums1
            for i in start..nums1.len() {
                if j < nums2.len() && nums1[i] + diff == nums2[j] {
                    j += 1;
                } else {
                    skipped += 1;
                    if skipped > 2 {
                        break;
                    }
                }
            }

            // 总跳过数 = start 之前的元素（必须跳过）+ 之后跳过的
            let total_skipped = start + skipped;
            if j == nums2.len() && total_skipped == 2 {
                ans = ans.min(diff);
            }
        }

        ans
    }
}
```
