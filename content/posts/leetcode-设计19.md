---
title: "leetcode-设计19"
date: 2026-07-14T10:41:25+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 子矩形查询

请你实现一个类 SubrectangleQueries ，它的构造函数的参数是一个 rows x cols 的矩形（这里用整数矩阵表示），并支持以下两种操作：

1. updateSubrectangle(int row1, int col1, int row2, int col2, int newValue)
    用 newValue 更新以 (row1,col1) 为左上角且以 (row2,col2) 为右下角的子矩形。

2. getValue(int row, int col)
    返回矩形中坐标 (row,col) 的当前值。


```
/// 子矩形查询：支持区间更新和单点查询
struct SubrectangleQueries {
    /// 原始矩形数据
    rectangle: Vec<Vec<i32>>,
}

impl SubrectangleQueries {
    /// 使用矩形初始化
    ///
    /// 时间复杂度: O(1)，空间复杂度: O(rows * cols)
    pub fn new(rectangle: Vec<Vec<i32>>) -> Self {
        Self { rectangle }
    }

    /// 将指定子矩形内的所有元素更新为 new_value
    ///
    /// # 参数
    /// - `row1, col1`: 子矩形左上角坐标
    /// - `row2, col2`: 子矩形右下角坐标
    /// - `new_value`: 更新后的值
    ///
    /// 时间复杂度: O((row2-row1+1) * (col2-col1+1))
    pub fn update_subrectangle(&mut self, row1: i32, col1: i32, row2: i32, col2: i32, new_value: i32) {
        let (r1, c1) = (row1 as usize, col1 as usize);
        let (r2, c2) = (row2 as usize, col2 as usize);

        // 遍历子矩形区域并更新
        for row in &mut self.rectangle[r1..=r2] {
            for cell in &mut row[c1..=c2] {
                *cell = new_value;
            }
        }
    }

    /// 获取矩形中指定坐标的值
    ///
    /// 时间复杂度: O(1)
    pub fn get_value(&self, row: i32, col: i32) -> i32 {
        self.rectangle[row as usize][col as usize]
    }
}
```
