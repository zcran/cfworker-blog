---
title: "leetcode-数组24"
date: 2026-10-10T20:38:42+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 可以形成最大正方形的矩形数目

给你一个数组 rectangles ，其中 rectangles[i] = [li, wi] 表示第 i 个矩形的长度为 li 、宽度为 wi 。

如果存在 k 同时满足 k <= li 和 k <= wi ，就可以将第 i 个矩形切成边长为 k 的正方形。例如，矩形 [4,6] 可以切成边长最大为 4 的正方形。

设 maxLen 为可以从矩形数组 rectangles 切分得到的 最大正方形 的边长。

请你统计有多少个矩形能够切出边长为 maxLen 的正方形，并返回矩形 数目 。


```
impl Solution {
    /// 统计能切出最大正方形的矩形数目。
    ///
    /// 每个矩形能切出的最大正方形边长为其长宽中的较小值 `min(l, w)`。
    /// 先求出所有矩形该值的最大值 `max_len`，
    /// 再统计有多少个矩形的 `min(l, w)` 等于 `max_len`。
    ///
    /// 时间复杂度 O(n)，空间复杂度 O(1)（不计输入）。
    pub fn count_good_rectangles(rectangles: Vec<Vec<i32>>) -> i32 {
        let mut max_len = 0;
        let mut count = 0;

        for rect in &rectangles {
            let side = rect[0].min(rect[1]);
            if side > max_len {
                max_len = side;
                count = 1;
            } else if side == max_len {
                count += 1;
            }
        }

        count
    }
}
```
