---
title: "leetcode-数组1"
date: 2026-10-10T20:38:40+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 插入区间

给你一个 无重叠的 ，按照区间起始端点排序的区间列表 intervals，其中 intervals[i] = [starti, endi] 表示第 i 个区间的开始和结束，并且 intervals 按照 starti 升序排列。同样给定一个区间 newInterval = [start, end] 表示另一个区间的开始和结束。

如果两个区间 至少 共享一个点，则认为它们是重叠的。

在 intervals 中插入区间 newInterval，使得 intervals 依然按照 starti 升序排列，且区间之间不重叠（如果有必要的话，可以合并区间）。

返回插入之后的 intervals。

注意 你不需要原地修改 intervals。你可以创建一个新数组然后返回它。


```
use std::cmp::Ordering;

impl Solution {
    /// 在有序、无重叠的区间列表中插入新区间，并合并所有重叠区间。
    ///
    /// 核心思路：
    /// 1. 用二分查找定位所有与 `new_interval` 重叠的区间范围 `[start, end)`；
    /// 2. 将这些区间合并为一个区间：左端取 `new_interval[0]` 与原区间最小左端，
    ///    右端取 `new_interval[1]` 与原区间最大右端；
    /// 3. 用 `splice` 原地替换，返回结果。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)（不计返回值）。
    pub fn insert(
        mut intervals: Vec<Vec<i32>>,
        new_interval: Vec<i32>,
    ) -> Vec<Vec<i32>> {
        // 找到第一个右端点 >= new_interval[0] 的区间（即第一个可能重叠的区间）
        // 以及第一个左端点 > new_interval[1] 的区间（即第一个不重叠的区间）
        let start = intervals.bisect_key_fn_greater_or_equal(
            &new_interval[0],
            |interval| &interval[1],
        );
        let end = intervals.bisect_key_fn_greater(
            &new_interval[1],
            |interval| &interval[0],
        );

        // 合并重叠区间：左端取所有重叠区间与新区间的最小左端，右端取最大右端
        let merged_start = new_interval[0].min(
            intervals.get(start).map_or(new_interval[0], |v| v[0]),
        );
        let merged_end = new_interval[1].max(
            intervals.get(end.wrapping_sub(1)).map_or(new_interval[1], |v| v[1]),
        );

        intervals.splice(start..end, [vec![merged_start, merged_end]]);
        intervals
    }
}

// ---------------------------------------------------------------------------
// 二分查找工具 trait（通用、零成本抽象）
// ---------------------------------------------------------------------------

pub trait Bisect<T> {
    /// 返回第一个满足谓词 `f` 的索引；若不存在则返回 `len`。
    fn bisect<F>(&self, f: F) -> usize
    where
        F: FnMut(&T) -> bool;
}

impl<T> Bisect<T> for [T] {
    #[inline]
    fn bisect<F>(&self, mut f: F) -> usize
    where
        F: FnMut(&T) -> bool,
    {
        let mut lo = 0;
        let mut hi = self.len();
        while lo < hi {
            let mid = (lo + hi) >> 1;
            if f(&self[mid]) {
                hi = mid;
            } else {
                lo = mid + 1;
            }
        }
        lo
    }
}

pub trait BisectFn<T>: Bisect<T> {
    /// 第一个 `key(x) > target` 的索引。
    #[inline]
    fn bisect_key_fn_greater<K, F>(&self, target: &K, mut key: F) -> usize
    where
        K: Ord,
        F: FnMut(&T) -> &K,
    {
        self.bisect(|x| key(x) > target)
    }

    /// 第一个 `key(x) >= target` 的索引。
    #[inline]
    fn bisect_key_fn_greater_or_equal<K, F>(&self, target: &K, mut key: F) -> usize
    where
        K: Ord,
        F: FnMut(&T) -> &K,
    {
        self.bisect(|x| key(x) >= target)
    }
}

impl<T> BisectFn<T> for [T] {}
```
