---
title: "leetcode-排序3"
date: 2026-10-05T00:34:59+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 最大间距

给定一个无序的数组 nums，返回 数组在排序之后，相邻元素之间最大的差值 。如果数组元素个数小于 2，则返回 0 。

您必须编写一个在「线性时间」内运行并使用「线性额外空间」的算法。


```
impl Solution {
    /// 线性时间求排序后相邻元素的最大差值（桶排序 / 鸽巢原理）
    ///
    /// 思路：将值域 [min, max] 均分为 n-1 个桶，桶宽 d = ceil((max-min)/(n-1))。
    /// 由鸽巢原理，最大间距必然出现在相邻的「非空」桶之间，
    /// 因此每个桶只需记录最小值和最大值，无需完整排序。
    pub fn maximum_gap(nums: Vec<i32>) -> i32 {
        let (min, max) = match nums.iter().fold((i32::MAX, i32::MIN), |(lo, hi), &x| {
            (lo.min(x), hi.max(x))
        }) {
            // 元素不足 2 个，或所有元素相同，最大间距为 0
            (lo, hi) if lo == hi => return 0,
            (lo, hi) => (lo, hi),
        };

        let n = nums.len();
        // 桶宽向上取整，保证答案至少为 d（最大值与最小值的间距 ≥ d）
        let d = ((max - min) as usize).div_ceil(n - 1);
        let bucket_count = (max - min) as usize / d + 1;

        // 每个桶记录 (最小值, 最大值)，初始化为空桶哨兵
        let mut buckets = vec![(i32::MAX, i32::MIN); bucket_count];
        for x in nums {
            let idx = (x - min) as usize / d;
            let bucket = &mut buckets[idx];
            bucket.0 = bucket.0.min(x);
            bucket.1 = bucket.1.max(x);
        }

        // 扫描相邻非空桶：当前桶最小值 - 上一个非空桶最大值
        let mut max_gap = 0;
        let mut prev_max = i32::MAX; // 哨兵：跳过第一个非空桶
        for &(lo, hi) in &buckets {
            if lo != i32::MAX {
                max_gap = max_gap.max(lo - prev_max);
                prev_max = hi;
            }
        }
        max_gap
    }
}
```
