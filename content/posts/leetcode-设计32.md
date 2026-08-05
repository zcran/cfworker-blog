---
title: "leetcode-设计32"
date: 2026-07-14T10:41:26+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 设计相邻元素求和服务

给你一个 n x n 的二维数组 grid，它包含范围 [0, n2 - 1] 内的不重复元素。

实现 neighborSum 类：

neighborSum(int [][]grid) 初始化对象。
int adjacentSum(int value) 返回在 grid 中与 value 相邻的元素之和，相邻指的是与 value 在上、左、右或下的元素。
int diagonalSum(int value) 返回在 grid 中与 value 对角线相邻的元素之和，对角线相邻指的是与 value 在左上、右上、左下或右下的元素。



```
use std::collections::HashMap;

/// 网格邻居求和器
///
/// 支持查询任意值的四邻域和八邻域（对角线）之和
/// 时间复杂度: 初始化 O(n²), 查询 O(1)
/// 空间复杂度: O(n²)
struct NeighborSum {
    grid: Vec<Vec<i32>>,          // 网格数据
    pos: HashMap<i32, (usize, usize)>, // 值 -> 坐标映射
}

impl NeighborSum {
    /// 初始化网格和位置映射
    fn new(grid: Vec<Vec<i32>>) -> Self {
        let mut pos = HashMap::with_capacity(grid.len() * grid[0].len());
        for (i, row) in grid.iter().enumerate() {
            for (j, &val) in row.iter().enumerate() {
                pos.insert(val, (i, j));
            }
        }
        NeighborSum { grid, pos }
    }

    /// 查询上下左右四个方向相邻元素之和
    fn adjacent_sum(&self, value: i32) -> i32 {
        const DIRS: [(i32, i32); 4] = [(-1, 0), (1, 0), (0, -1), (0, 1)];
        self.sum_neighbors(value, &DIRS)
    }

    /// 查询四个对角线方向相邻元素之和
    fn diagonal_sum(&self, value: i32) -> i32 {
        const DIRS: [(i32, i32); 4] = [(-1, -1), (-1, 1), (1, -1), (1, 1)];
        self.sum_neighbors(value, &DIRS)
    }

    /// 通用邻居求和函数
    fn sum_neighbors(&self, value: i32, dirs: &[(i32, i32)]) -> i32 {
        // 获取坐标，如果值不存在则返回 0
        let &(x, y) = match self.pos.get(&value) {
            Some(pos) => pos,
            None => return 0,
        };

        let rows = self.grid.len() as i32;
        let cols = self.grid[0].len() as i32;

        let mut sum = 0;
        for &(dx, dy) in dirs {
            let nx = x as i32 + dx;
            let ny = y as i32 + dy;
            if nx >= 0 && nx < rows && ny >= 0 && ny < cols {
                sum += self.grid[nx as usize][ny as usize];
            }
        }
        sum
    }
}
```
