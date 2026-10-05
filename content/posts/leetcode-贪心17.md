---
title: "leetcode-贪心17"
date: 2026-10-03T12:55:35+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 数组列表中的最大距离

给定 m 个数组，每个数组都已经按照升序排好序了。

现在你需要从两个不同的数组中选择两个整数（每个数组选一个）并且计算它们的距离。两个整数 a 和 b 之间的距离定义为它们差的绝对值 |a-b| 。

返回最大距离。


```
impl Solution {
    pub fn max_distance(arrays: Vec<Vec<i32>>) -> i32 {
        // 每个数组已升序：最小值在首、最大值在尾
        // 贪心：维护此前所有数组的最小值和最大值，逐个与当前数组的最大/最小配对
        let (mut min_val, mut max_val) = (arrays[0][0], *arrays[0].last().unwrap());
        let mut ans = 0;

        for arr in &arrays[1..] {
            let (first, last) = (arr[0], *arr.last().unwrap());
            // 当前数组最大值 与 之前最小值 的距离
            ans = ans.max(last - min_val);
            // 之前最大值 与 当前数组最小值 的距离
            ans = ans.max(max_val - first);
            // 更新全局最小/最大值
            min_val = min_val.min(first);
            max_val = max_val.max(last);
        }

        ans
    }
}
```
