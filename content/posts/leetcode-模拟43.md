---
title: "leetcode-模拟43"
date: 2026-08-08T11:31:11+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 球会落何处

用一个大小为 m x n 的二维网格 grid 表示一个箱子。你有 n 颗球。箱子的顶部和底部都是开着的。

箱子中的每个单元格都有一个对角线挡板，跨过单元格的两个角，可以将球导向左侧或者右侧。

将球导向右侧的挡板跨过左上角和右下角，在网格中用 1 表示。
将球导向左侧的挡板跨过右上角和左下角，在网格中用 -1 表示。

在箱子每一列的顶端各放一颗球。每颗球都可能卡在箱子里或从底部掉出来。如果球恰好卡在两块挡板之间的 "V" 形图案，或者被一块挡导向到箱子的任意一侧边上，就会卡住。

返回一个大小为 n 的数组 answer ，其中 answer[i] 是球放在顶部的第 i 列后从底部掉出来的那一列对应的下标，如果球卡在盒子里，则返回 -1 。


```
impl Solution {
    pub fn find_ball(grid: Vec<Vec<i32>>) -> Vec<i32> {
        let n = grid[0].len();
        (0..n)
            .map(|mut col| {
                for row in &grid {
                    let dir = row[col];
                    let next = col as i32 + dir;
                    // 出界（撞墙）或 V 形（相邻挡板反向）
                    if next < 0 || next as usize >= n || row[next as usize] != dir {
                        return -1;
                    }
                    col = next as usize;
                }
                col as i32
            })
            .collect()
    }
}
```
