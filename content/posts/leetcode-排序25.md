---
title: "leetcode-排序25"
date: 2026-10-05T00:35:01+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 删除被覆盖区间

给你一个区间列表，请你删除列表中被其他区间所覆盖的区间。

只有当 c <= a 且 b <= d 时，我们才认为区间 [a,b) 被区间 [c,d) 覆盖。

在完成所有删除操作后，请你返回列表中剩余区间的数目。




```
impl Solution {
    /// 删除被其他区间覆盖的区间，返回剩余区间数
    ///
    /// 思路：按左端点升序、左端点相同则右端点降序排序。
    /// 这样遍历时，只要当前区间的右端点 ≤ 之前见过的最大右端点 rmax，
    /// 就说明它被某个区间覆盖；否则更新 rmax 并保留。
    /// 左端点相同时右端点降序，保证大区间排在小区间之前，被正确计入覆盖。
    /// 时间 O(n log n)，额外空间 O(1)。
    pub fn remove_covered_intervals(mut intervals: Vec<Vec<i32>>) -> i32 {
        // 左端点升序；左端点相同时右端点降序，使覆盖区间排在前面
        intervals.sort_unstable_by(|a, b| {
            a[0].cmp(&b[0]).then_with(|| b[1].cmp(&a[1]))
        });

        let mut remaining = 0;
        let mut rmax = i32::MIN; // 已保留区间的最大右端点

        for interval in &intervals {
            let r = interval[1];
            // 右端点严格大于当前最大右端点才不被覆盖
            if r > rmax {
                rmax = r;
                remaining += 1;
            }
        }

        remaining
    }
}
```
