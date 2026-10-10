---
title: "leetcode-排序64"
date: 2026-10-05T00:35:04+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 最大化网格图中正方形空洞的面积

给你两个整数 n 和 m，以及两个整数数组 hBars 和 vBars。网格由 n + 2 条水平线和 m + 2 条竖直线组成，形成 1x1 的单元格。网格中的线条从 1 开始编号。

你可以从 hBars 中 删除 一些水平线条，并从 vBars 中删除一些竖直线条。注意，其他线条是固定的，无法删除。

返回一个整数表示移除一些线条（可以不移除任何线条）后，网格中 正方形空洞的最大面积 。




```
impl Solution {
    pub fn maximize_square_hole_area(
        _n: i32,
        _m: i32,
        mut h_bars: Vec<i32>,
        mut v_bars: Vec<i32>,
    ) -> i32 {
        // 找出数组中最长的连续整数序列长度
        fn max_consecutive(bars: &mut Vec<i32>) -> i32 {
            bars.sort_unstable();
            let mut max_len = 1;
            let mut cur_len = 1;
            for w in bars.windows(2) {
                cur_len = if w[1] == w[0] + 1 { cur_len + 1 } else { 1 };
                max_len = max_len.max(cur_len);
            }
            max_len
        }

        // 删除一段连续 k 条内部线，可以形成宽度为 k + 1 的空洞
        // 正方形空洞边长取水平、竖直方向可形成的最大边长中的较小值
        let side = max_consecutive(&mut h_bars).min(max_consecutive(&mut v_bars)) + 1;
        side * side
    }
}
```
