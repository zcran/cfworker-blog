---
title: "leetcode-贪心131"
date: 2026-10-03T12:55:42+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 数组的最小相等和

给你两个由正整数和 0 组成的数组 nums1 和 nums2 。

你必须将两个数组中的 所有 0 替换为 严格 正整数，并且满足两个数组中所有元素的和 相等 。

返回 最小 相等和 ，如果无法使两数组相等，则返回 -1 。




```
impl Solution {
    pub fn min_sum(nums1: Vec<i32>, nums2: Vec<i32>) -> i64 {
        // 把每个 0 先当成 1（最小值），得到各数组的最小可能和，
        // 同时记录 0 的个数（决定该和还能往上提升多少）。
        let calc = |nums: &Vec<i32>| -> (i64, i32) {
            let mut sum = 0i64;
            let mut zeros = 0;
            for &x in nums {
                if x == 0 {
                    sum += 1;
                    zeros += 1;
                } else {
                    sum += x as i64;
                }
            }
            (sum, zeros)
        };

        let (sum1, zero1) = calc(&nums1);
        let (sum2, zero2) = calc(&nums2);

        // 若一边没有 0（和固定），而另一边的最小和已超过它，则无法相等
        if (zero1 == 0 && sum2 > sum1) || (zero2 == 0 && sum1 > sum2) {
            return -1;
        }

        // 取两个最小和中较大者即为最小相等和
        sum1.max(sum2)
    }
}
```
