---
title: "leetcode-排序71"
date: 2026-10-05T00:35:04+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 判断网格图能否被切割成块

给你一个整数 n 表示一个 n x n 的网格图，坐标原点是这个网格图的左下角。同时给你一个二维坐标数组 rectangles ，其中 rectangles[i] 的格式为 [startˇx, startˇy, endˇx, endˇy] ，表示网格图中的一个矩形。每个矩形定义如下：

(startˇx, startˇy)：矩形的左下角。
(endˇx, endˇy)：矩形的右上角。

注意 ，矩形相互之间不会重叠。你的任务是判断是否能找到两条 要么都垂直要么都水平 的 两条切割线 ，满足：

切割得到的三个部分分别都 至少 包含一个矩形。
每个矩形都 恰好仅 属于一个切割得到的部分。

如果可以得到这样的切割，请你返回 true ，否则返回 false 。




```
impl Solution {
    pub fn check_valid_cuts(_n: i32, rectangles: Vec<Vec<i32>>) -> bool {
        // 判断一维投影能否被切成 3 段（即存在 2 条不相交的切割线）
        // 每段至少包含一个矩形，等价于合并区间后至少存在 3 个互不重叠的区间
        fn can_split(mut intervals: Vec<(i32, i32)>) -> bool {
            intervals.sort_unstable();
            let mut sections = 0;
            let mut covered = i32::MIN; // 已合并区间覆盖到的最右端
            for (l, r) in intervals {
                if l >= covered {
                    sections += 1;
                }
                covered = covered.max(r);
            }
            sections >= 3
        }

        // 提取 x 方向（左右边界）和 y 方向（上下边界）的一维投影
        let x_intervals: Vec<(i32, i32)> = rectangles.iter().map(|v| (v[0], v[2])).collect();
        let y_intervals: Vec<(i32, i32)> = rectangles.iter().map(|v| (v[1], v[3])).collect();

        // 只要任一方向能切成 3 段，就存在两条平行的有效切割线
        can_split(x_intervals) || can_split(y_intervals)
    }
}
```
