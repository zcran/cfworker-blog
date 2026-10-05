---
title: "leetcode-贪心54"
date: 2026-10-03T12:55:37+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 切割后面积最大的蛋糕

矩形蛋糕的高度为 h 且宽度为 w，给你两个整数数组 horizontalCuts 和 verticalCuts，其中：

 horizontalCuts[i] 是从矩形蛋糕顶部到第  i 个水平切口的距离

 verticalCuts[j] 是从矩形蛋糕的左侧到第 j 个竖直切口的距离

请你按数组 horizontalCuts 和 verticalCuts 中提供的水平和竖直位置切割后，请你找出 面积最大 的那份蛋糕，并返回其 面积 。由于答案可能是一个很大的数字，因此需要将结果 对 10^9 + 7 取余 后返回。




```
impl Solution {
    pub fn max_area(h: i32, w: i32, horizontal_cuts: Vec<i32>, vertical_cuts: Vec<i32>) -> i32 {
        let max_h = Self::max_gap(h, horizontal_cuts);
        let max_w = Self::max_gap(w, vertical_cuts);
        // 用 i64 相乘防止溢出，再取模
        (max_h as i64 * max_w as i64 % 1_000_000_007) as i32
    }

    /// 在 [0, size] 的区间上，加入若干切割点后返回相邻点之间的最大间距
    fn max_gap(size: i32, mut cuts: Vec<i32>) -> i32 {
        cuts.sort_unstable();
        // 首段：0 到第一个切口；尾段：最后一个切口到 size
        let mut max = cuts[0].max(size - cuts[cuts.len() - 1]);
        // 中间各段
        for i in 1..cuts.len() {
            max = max.max(cuts[i] - cuts[i - 1]);
        }
        max
    }
}
```
