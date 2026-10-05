---
title: "leetcode-贪心156"
date: 2026-10-03T12:55:44+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 最小相邻交换至奇偶交替

给你一个由互不相同的整数组成的数组 nums 。

在一次操作中，你可以交换任意两个 相邻 元素。

在一个排列中，当所有相邻元素的奇偶性交替出现，我们认为该排列是 有效排列。这意味着每对相邻元素中一个是偶数，一个是奇数。

请返回将 nums 变成任意一种 有效排列 所需的最小相邻交换次数。

如果无法重排 nums 来获得有效排列，则返回 -1。


```
impl Solution {
    pub fn min_swaps(nums: Vec<i32>) -> i32 {
        // 奇偶交替排列只有两种可能：奇开头 或 偶开头。
        // 相邻交换的最小次数 = 把奇数（或偶数）移动到目标位置的位移之和。
        let n = nums.len();
        let odd_count = nums.iter().filter(|&&x| x % 2 != 0).count();
        let even_count = n - odd_count;

        // 奇偶数量差超过 1 时无法交替
        if (odd_count as i32 - even_count as i32).abs() > 1 {
            return -1;
        }

        // 计算把奇数放到所有目标位置所需的最小交换次数。
        // 起始偏移为 start（目标位置中奇数的第一个下标）。
        let count_odd_at = |start: usize| -> i32 {
            let mut cost = 0;
            let mut pos = start;
            for (i, &x) in nums.iter().enumerate() {
                if x % 2 != 0 {
                    cost += (i as i32 - pos as i32).abs();
                    pos += 2;
                }
            }
            cost
        };

        match odd_count.cmp(&even_count) {
            // 奇数多一个：必须以奇数开头
            std::cmp::Ordering::Greater => count_odd_at(0),
            // 偶数多一个：必须以偶数开头，奇数从下标 1 开始
            std::cmp::Ordering::Less => count_odd_at(1),
            // 数量相等：两种开头都可行，取较小者
            std::cmp::Ordering::Equal => count_odd_at(0).min(count_odd_at(1)),
        }
    }
}
```
