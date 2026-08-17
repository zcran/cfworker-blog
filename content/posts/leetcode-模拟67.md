---
title: "leetcode-模拟67"
date: 2026-08-08T11:31:12+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 统计可以提取的工件

存在一个 n x n 大小、下标从 0 开始的网格，网格中埋着一些工件。给你一个整数 n 和一个下标从 0 开始的二维整数数组 artifacts ，artifacts 描述了矩形工件的位置，其中 artifacts[i] = [r1i, c1i, r2i, c2i] 表示第 i 个工件在子网格中的填埋情况：

· (r1i, c1i) 是第 i 个工件 左上 单元格的坐标，且
· (r2i, c2i) 是第 i 个工件 右下 单元格的坐标。

你将会挖掘网格中的一些单元格，并清除其中的填埋物。如果单元格中埋着工件的一部分，那么该工件这一部分将会裸露出来。如果一个工件的所有部分都都裸露出来，你就可以提取该工件。

给你一个下标从 0 开始的二维整数数组 dig ，其中 dig[i] = [ri, ci] 表示你将会挖掘单元格 (ri, ci) ，返回你可以提取的工件数目。

生成的测试用例满足：

· 不存在重叠的两个工件。
· 每个工件最多只覆盖 4 个单元格。
· dig 中的元素互不相同。


```
use std::collections::HashSet;

impl Solution {
    /// 返回所有单元格都被挖掘的工件数量。
    /// 将挖掘位置存入 HashSet 以实现 O(1) 查询，
    /// 然后对每个工件检查其覆盖的每个单元格是否都被挖掘。
    pub fn dig_artifacts(_n: i32, artifacts: Vec<Vec<i32>>, dig: Vec<Vec<i32>>) -> i32 {
        let dig: HashSet<(i32, i32)> = dig.into_iter().map(|v| (v[0], v[1])).collect();
        artifacts
            .iter()
            .filter(|a| {
                (a[0]..=a[2]).all(|r| {
                    (a[1]..=a[3]).all(|c| dig.contains(&(r, c)))
                })
            })
            .count() as i32
    }
}
```
