---
title: "leetcode-堆11"
date: 2026-08-21T09:54:11+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 第 K 个最小的质数分数

给你一个按递增顺序排序的数组 arr 和一个整数 k 。数组 arr 由 1 和若干 质数 组成，且其中所有整数互不相同。

对于每对满足 0 <= i < j < arr.length 的 i 和 j ，可以得到分数 arr[i] / arr[j] 。

那么第 k 个最小的分数是多少呢?  以长度为 2 的整数数组返回你的答案, 这里 answer[0] == arr[i] 且 answer[1] == arr[j] 。


```
impl Solution {
    /// 二分搜索分数值，双指针统计小于等于 mid 的分数个数
    pub fn kth_smallest_prime_fraction(arr: Vec<i32>, k: i32) -> Vec<i32> {
        let n = arr.len();
        let k = k as usize;
        let mut left = 0.0;
        let mut right = 1.0;

        while left < right {
            let mid = (left + right) / 2.0;
            let mut count = 0;          // 小于等于 mid 的分数个数
            let mut best_num = 0;       // 这些分数中最大的那个（即最接近 mid 的）
            let mut best_den = 1;

            // 双指针：对每个分子 i，找最小的分母 j 使得 arr[i]/arr[j] <= mid
            let mut j = 1;
            for i in 0..n {
                while j < n && arr[i] as f64 > mid * arr[j] as f64 {
                    j += 1;
                }
                if j == n {
                    break;
                }
                // 从 j 到 n-1 的所有分母都满足 arr[i]/arr[j] <= mid
                count += n - j;
                // 记录这些分数中最大的（最接近 mid 的），用于最终答案
                if best_num * arr[j] < best_den * arr[i] {
                    best_num = arr[i];
                    best_den = arr[j];
                }
            }

            match count.cmp(&k) {
                std::cmp::Ordering::Equal => return vec![best_num, best_den],
                std::cmp::Ordering::Less => left = mid,
                std::cmp::Ordering::Greater => right = mid,
            }
        }

        vec![] // 理论上不会到达这里
    }
}
```
