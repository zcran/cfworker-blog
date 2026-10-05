---
title: "leetcode-贪心107"
date: 2026-10-03T12:55:41+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 使数组相似的最少操作次数

给你两个正整数数组 nums 和 target ，两个数组长度相等。

在一次操作中，你可以选择两个 不同 的下标 i 和 j ，其中 0 <= i, j < nums.length ，并且：

令 nums[i] = nums[i] + 2 且
令 nums[j] = nums[j] - 2 。

如果两个数组中每个元素出现的频率相等，我们称两个数组是 相似 的。

请你返回将 nums 变得与 target 相似的最少操作次数。测试数据保证 nums 一定能变得与 target 相似。


```
impl Solution {
    pub fn make_similar(mut nums: Vec<i32>, mut target: Vec<i32>) -> i64 {
        // 奇偶性不同的元素无法互相转化，因此分开处理：
        // 把奇数取负后排序，奇偶就会自动分成两段（负数段为奇数，正数段为偶数）。
        fn normalize(a: &mut Vec<i32>) {
            for x in a.iter_mut() {
                if *x % 2 == 1 {
                    *x = -*x;
                }
            }
            a.sort_unstable();
        }

        normalize(&mut nums);
        normalize(&mut target);

        // 每对元素差值绝对值和 / 4 即为最少操作次数：
        // 一次操作改变两个元素各 2，共影响 4。
        nums.iter()
            .zip(target.iter())
            .map(|(&a, &b)| (a - b).abs() as i64)
            .sum::<i64>()
            / 4
    }
}
```
