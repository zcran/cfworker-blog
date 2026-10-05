---
title: "leetcode-贪心145"
date: 2026-10-03T12:55:43+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 切蛋糕的最小总开销 II

有一个 m x n 大小的矩形蛋糕，需要切成 1 x 1 的小块。

给你整数 m ，n 和两个数组：

horizontalCut 的大小为 m - 1 ，其中 horizontalCut[i] 表示沿着水平线 i 切蛋糕的开销。
verticalCut 的大小为 n - 1 ，其中 verticalCut[j] 表示沿着垂直线 j 切蛋糕的开销。

一次操作中，你可以选择任意不是 1 x 1 大小的矩形蛋糕并执行以下操作之一：

沿着水平线 i 切开蛋糕，开销为 horizontalCut[i] 。
沿着垂直线 j 切开蛋糕，开销为 verticalCut[j] 。

每次操作后，这块蛋糕都被切成两个独立的小蛋糕。

每次操作的开销都为最开始对应切割线的开销，并且不会改变。

请你返回将蛋糕全部切成 1 x 1 的蛋糕块的 最小 总开销。


```
impl Solution {
    pub fn minimum_cost(m: i32, n: i32, horizontal_cut: Vec<i32>, vertical_cut: Vec<i32>) -> i64 {
        // 贪心：切割开销越大越应该先切（此时它影响的块数最少）。
        // h, v 分别表示当前水平/垂直方向的蛋糕块数。
        // 每次选开销较大的方向切一刀，开销乘以"另一方向的块数"。
        let mut h_cut = horizontal_cut;
        let mut v_cut = vertical_cut;
        h_cut.sort_unstable();
        v_cut.sort_unstable();

        let mut h = 1i64; // 水平方向现有块数
        let mut v = 1i64; // 垂直方向现有块数
        let mut cost = 0i64;

        while !h_cut.is_empty() || !v_cut.is_empty() {
            // 比较两个方向当前最大开销，选较大的先切
            let take_h = match (h_cut.last(), v_cut.last()) {
                (Some(&a), Some(&b)) => a > b,
                (Some(_), None) => true,
                _ => false,
            };

            if take_h {
                cost += h_cut.pop().unwrap() as i64 * h;
                v += 1; // 切一刀水平线，垂直方向块数 +1
            } else {
                cost += v_cut.pop().unwrap() as i64 * v;
                h += 1; // 切一刀垂直线，水平方向块数 +1
            }
        }

        // 说明：上面 h/v 的语义是"另一方向需要乘的块数"，
        // 即水平切割开销乘当前水平方向块数 h，垂直切割乘 v。
        let _ = m;
        let _ = n;
        cost
    }
}
```
