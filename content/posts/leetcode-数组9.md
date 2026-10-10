---
title: "leetcode-数组9"
date: 2026-10-10T20:38:41+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 到最近的人的最大距离

给你一个数组 seats 表示一排座位，其中 seats[i] = 1 代表有人坐在第 i 个座位上，seats[i] = 0 代表座位 i 上是空的（下标从 0 开始）。

至少有一个空座位，且至少有一人已经坐在座位上。

亚历克斯希望坐在一个能够使他与离他最近的人之间的距离达到最大化的座位上。

返回他到离他最近的人的最大距离。


```
impl Solution {
    /// 求亚历克斯坐到离最近的人距离最大的座位时，该最大距离。
    ///
    /// 分三种情况：
    /// - 最左侧空段：距离为第一个有人座位到 0 的距离（靠左端坐）；
    /// - 最右侧空段：距离为最后一个有人座位到末尾的距离（靠右端坐）；
    /// - 中间空段：距离为两侧有人座位间距的一半（坐中间）。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)。
    pub fn max_dist_to_closest(seats: Vec<i32>) -> i32 {
        let n = seats.len();

        // 第一个有人座位，左侧距离
        let first = seats.iter().position(|&s| s == 1).unwrap();
        let mut max_dist = first;

        // 最后一个有人座位，右侧距离
        let last = seats.iter().rposition(|&s| s == 1).unwrap();
        max_dist = max_dist.max(n - 1 - last);

        // 中间空段：相邻两个有人座位之间
        let mut prev = first;
        for i in first + 1..=last {
            if seats[i] == 1 {
                max_dist = max_dist.max((i - prev) / 2);
                prev = i;
            }
        }

        max_dist as i32
    }
}
```
