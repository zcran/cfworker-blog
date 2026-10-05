---
title: "leetcode-贪心45"
date: 2026-10-03T12:55:37+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 灌溉花园的最少水龙头数目

在 x 轴上有一个一维的花园。花园长度为 n，从点 0 开始，到点 n 结束。

花园里总共有 n + 1 个水龙头，分别位于 [0, 1, ..., n] 。

给你一个整数 n 和一个长度为 n + 1 的整数数组 ranges ，其中 ranges[i] （下标从 0 开始）表示：如果打开点 i 处的水龙头，可以灌溉的区域为 [i -  ranges[i], i + ranges[i]] 。

请你返回可以灌溉整个花园的 最少水龙头数目 。如果花园始终存在无法灌溉到的地方，请你返回 -1 。


```
impl Solution {
    pub fn min_taps(n: i32, ranges: Vec<i32>) -> i32 {
        // right_most[i]：所有能覆盖点 i 的水龙头中，能到达的最右位置
        let mut right_most = vec![0; n as usize + 1];
        for (i, &r) in ranges.iter().enumerate() {
            let left = (i as i32 - r).max(0) as usize;
            right_most[left] = right_most[left].max(i as i32 + r);
        }

        // 贪心跳跃：维护当前区间右端 curr_right 与下一跳能到的最远位置 next_right
        let mut ans = 0;
        let mut curr_right = 0;
        let mut next_right = 0;
        for i in 0..n {
            next_right = next_right.max(right_most[i as usize]);
            // 当前位置无法被任何水龙头覆盖，说明存在断点
            if next_right <= i {
                return -1;
            }
            // 已走到当前区间的尽头，必须再开一个水龙头
            if curr_right == i {
                curr_right = next_right;
                ans += 1;
            }
        }

        ans
    }
}
```
