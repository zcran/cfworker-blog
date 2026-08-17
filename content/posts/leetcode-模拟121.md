---
title: "leetcode-模拟121"
date: 2026-08-08T11:31:16+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 跳过交替单元格的之字形遍历

给你一个 m x n 的二维数组 grid，数组由 正整数 组成。

你的任务是以 之字形 遍历 grid，同时跳过每个 交替 的单元格。

之字形遍历的定义如下：

从左上角的单元格 (0, 0) 开始。
在当前行中向 右 移动，直到到达该行的末尾。
下移到下一行，然后在该行中向 左 移动，直到到达该行的开头。
继续在行间交替向右和向左移动，直到所有行都被遍历完。

注意：在遍历过程中，必须跳过每个 交替 的单元格。

返回一个整数数组 result，其中包含按 顺序 记录的、且跳过交替单元格后的之字形遍历中访问到的单元格值。


```
impl Solution {
    /// 以之字形遍历二维数组，并跳过交替的单元格
    ///
    /// 遍历规则：
    /// - 偶数行（0, 2, 4...）：从左到右
    /// - 奇数行（1, 3, 5...）：从右到左
    /// - 跳过交替单元格：即每隔一个取一个（step_by(2)）
    ///
    /// # 参数
    /// - `grid`: m x n 的二维正整数数组
    ///
    /// # 返回值
    /// - 按顺序记录的访问到的单元格值
    pub fn zigzag_traversal(grid: Vec<Vec<i32>>) -> Vec<i32> {
        let m = grid.len();

        (0..m)
            .flat_map(|i| {
                // 偶数行保持原序，奇数行反转
                let row_iter: Box<dyn Iterator<Item = i32>> = if i % 2 == 0 {
                    Box::new(grid[i].iter().copied())
                } else {
                    Box::new(grid[i].iter().copied().rev())
                };
                row_iter
            })
            .enumerate()
            .filter_map(|(idx, val)| if idx % 2 == 0 { Some(val) } else { None })
            .collect()
    }
}
```
