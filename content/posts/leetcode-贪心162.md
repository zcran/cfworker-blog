---
title: "leetcode-贪心162"
date: 2026-10-03T12:55:45+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 转换数组的最少操作次数

给你两个整数数组，第一个数组 nums1 长度为 n，以及第二个数组 nums2 长度为 n + 1。

你的目标是使用 最少 的操作次数将 nums1 转换为 nums2。

你可以执行以下操作 任意 次，每次选择一个下标 i：

将 nums1[i] 增加 1。
将 nums1[i] 减少 1。
将 nums1[i] 追加 到数组的 末尾 。

返回将 nums1 转换为 nums2 所需的 最少 操作次数。


```
impl Solution {
    pub fn min_operations(nums1: Vec<i32>, nums2: Vec<i32>) -> i64 {
        // nums2 比 nums1 多一个元素，即 nums2.back() 是需要"追加"的目标值。
        // 基础代价：把每个 nums1[i] 调整到 nums2[i]，加上一次追加操作。
        // 关键优化：在某个 nums1[i] 调整到 nums2[i] 的过程中，
        // 可以"顺路"把 target 变出来并追加，从而省掉追加的开销。
        // 取所有位置中"顺路代价"最小者，若该代价 <= 0 则追加免费。
        let target = nums2[nums2.len() - 1] as i64;
        let mut ans: i64 = 1; // 追加操作本身至少一次
        let mut extra = i64::MAX;

        for i in 0..nums1.len() {
            let x = nums1[i] as i64;
            let y = nums2[i] as i64;
            let (lo, hi) = if x < y { (x, y) } else { (y, x) };

            ans += hi - lo;

            // target 落在 [lo, hi] 内代价为 0，否则为到最近端点的距离
            let cost = (lo - target).max(target - hi);
            extra = extra.min(cost);
        }

        ans + extra.max(0)
    }
}
```
