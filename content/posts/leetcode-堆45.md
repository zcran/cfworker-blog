---
title: "leetcode-堆45"
date: 2026-08-21T09:54:13+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 最小差值平方和

给你两个下标从 0 开始的整数数组 nums1 和 nums2 ，长度为 n 。

数组 nums1 和 nums2 的 差值平方和 定义为所有满足 0 <= i < n 的 (nums1[i] - nums2[i])^2 之和。

同时给你两个正整数 k1 和 k2 。你可以将 nums1 中的任意元素 +1 或者 -1 至多 k1 次。类似的，你可以将 nums2 中的任意元素 +1 或者 -1 至多 k2 次。

请你返回修改数组 nums1 至多 k1 次且修改数组 nums2 至多 k2 次后的最小 差值平方和 。

注意：你可以将数组中的元素变成 负 整数。


```
impl Solution {
    pub fn min_sum_square_diff(nums1: Vec<i32>, nums2: Vec<i32>, k1: i32, k2: i32) -> i64 {
        // Total operations available to reduce differences
        let mut ops = (k1 + k2) as i64;

        // Absolute differences between corresponding elements
        let mut diffs: Vec<i32> = nums1.into_iter()
            .zip(nums2)
            .map(|(a, b)| (a - b).abs())
            .collect();
        diffs.sort_unstable_by(|a, b| b.cmp(a));

        let max_diff = diffs[0] as usize;
        let mut freq = vec![0i64; max_diff + 1];

        for &d in &diffs {
            freq[d as usize] += 1;
        }

        // Greedily reduce the largest differences first
        for i in (1..=max_diff).rev() {
            if ops <= 0 {
                break;
            }
            let reduce = freq[i].min(ops);
            freq[i] -= reduce;
            freq[i - 1] += reduce;
            ops -= reduce;
        }

        // Compute sum of squares; any remaining ops are wasted (diff already 0)
        freq.into_iter()
            .enumerate()
            .map(|(d, cnt)| cnt * d as i64 * d as i64)
            .sum()
    }
}
```
