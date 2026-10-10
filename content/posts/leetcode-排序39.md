---
title: "leetcode-排序39"
date: 2026-10-05T00:35:02+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 等差子数组

如果一个数列由至少两个元素组成，且每两个连续元素之间的差值都相同，那么这个序列就是 等差数列 。更正式地，数列 s 是等差数列，只需要满足：对于每个有效的 i ， s[i+1] - s[i] == s[1] - s[0] 都成立。

例如，下面这些都是 等差数列 ：

1, 3, 5, 7, 9
7, 7, 7, 7
3, -1, -5, -9

下面的数列 不是等差数列 ：

1, 1, 2, 5, 7

给你一个由 n 个整数组成的数组 nums，和两个由 m 个整数组成的数组 l 和 r，后两个数组表示 m 组范围查询，其中第 i 个查询对应范围 [l[i], r[i]] 。所有数组的下标都是 从 0 开始 的。

返回 boolean 元素构成的答案列表 answer 。如果子数组 nums[l[i]], nums[l[i]+1], ... , nums[r[i]] 可以 重新排列 形成 等差数列 ，answer[i] 的值就是 true；否则answer[i] 的值就是 false 。


```
use std::collections::HashSet;

impl Solution {
    /// 判断每个查询区间能否重排成等差数列
    ///
    /// 思路：一个可重排成等差数列的集合需满足：
    ///   1. 极差 (max - min) 能被 (len - 1) 整除，得公差 d；
    ///   2. d == 0 时全部元素相同（集合大小 1）；
    ///   3. d > 0 时，min, min+d, ..., max 每个值都出现在集合中（保证无重复、无缺失）。
    /// 因此每个区间只需一次扫描求极差与去重集合，再做整除与等差覆盖检查。
    /// 时间 O(sum 区间长度)，额外空间 O(区间长度)。
    pub fn check_arithmetic_subarrays(nums: Vec<i32>, l: Vec<i32>, r: Vec<i32>) -> Vec<bool> {
        l.iter()
            .zip(&r)
            .map(|(&left, &right)| {
                let (lo, hi) = (left as usize, right as usize);
                let len = hi - lo; // 区间长度 - 1

                let mut min = nums[lo];
                let mut max = nums[lo];
                let mut set = HashSet::with_capacity(len + 1);

                for &x in &nums[lo..=hi] {
                    min = min.min(x);
                    max = max.max(x);
                    set.insert(x);
                }

                // 极差必须能被间隔数整除
                let span = max - min;
                if span % len as i32 != 0 {
                    return false;
                }

                let d = span / len as i32;
                if d == 0 {
                    // 所有元素相同 ⇔ 去重后只有 1 个
                    return set.len() == 1;
                }

                // 等差序列的每个值都必须存在
                (0..=len as i32).all(|k| set.contains(&(min + k * d)))
            })
            .collect()
    }
}
```
