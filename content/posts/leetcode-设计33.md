---
title: "leetcode-设计33"
date: 2026-07-14T10:41:26+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 设计电子表格

电子表格是一个网格，它有 26 列（从 'A' 到 'Z'）和指定数量的 rows。每个单元格可以存储一个 0 到 105 之间的整数值。

请你实现一个 Spreadsheet 类：

· Spreadsheet(int rows) 初始化一个具有 26 列（从 'A' 到 'Z'）和指定行数的电子表格。所有单元格最初的值都为 0 。
· void setCell(String cell, int value) 设置指定单元格的值。单元格引用以 "AX" 的格式提供（例如，"A1"，"B10"），其中字母表示列（从 'A' 到 'Z'），数字表示从 1 开始的行号。
· void resetCell(String cell) 重置指定单元格的值为 0 。
· int getValue(String formula) 计算一个公式的值，格式为 "=X+Y"，其中 X 和 Y 要么 是单元格引用，要么非负整数，返回计算的和。

注意： 如果 getValue 引用一个未通过 setCell 明确设置的单元格，则该单元格的值默认为 0 。


```
/// 电子表格
///
/// 支持设置/重置单元格值，计算加法公式（支持单元格引用和数字）
/// 时间复杂度: 所有操作 O(1)
/// 空间复杂度: O(rows * 26)
pub struct Spreadsheet {
    grid: Vec<[i32; 26]>, // 行索引从 1 开始，0 行不使用
}

impl Spreadsheet {
    /// 初始化 rows 行 × 26 列的电子表格
    pub fn new(rows: i32) -> Self {
        Spreadsheet {
            grid: vec![[0; 26]; (rows + 1) as usize],
        }
    }

    /// 设置指定单元格的值
    pub fn set_cell(&mut self, cell: String, value: i32) {
        let (row, col) = Self::parse_cell(&cell);
        self.grid[row][col] = value;
    }

    /// 重置指定单元格为 0
    pub fn reset_cell(&mut self, cell: String) {
        let (row, col) = Self::parse_cell(&cell);
        self.grid[row][col] = 0;
    }

    /// 计算公式 "=X+Y" 的值
    pub fn get_value(&self, formula: String) -> i32 {
        let plus_idx = formula.find('+').unwrap();
        let left = &formula[1..plus_idx];  // 跳过 '='
        let right = &formula[plus_idx + 1..];
        self.parse_value(left) + self.parse_value(right)
    }

    /// 解析单元格引用，返回 (行, 列) 索引
    fn parse_cell(cell: &str) -> (usize, usize) {
        let row = cell[1..].parse::<usize>().unwrap();
        let col = (cell.chars().next().unwrap() as u8 - b'A') as usize;
        (row, col)
    }

    /// 解析值：数字直接返回，单元格引用则查询 grid
    fn parse_value(&self, token: &str) -> i32 {
        if token.chars().next().unwrap().is_ascii_alphabetic() {
            let (row, col) = Self::parse_cell(token);
            self.grid[row][col]
        } else {
            token.parse().unwrap()
        }
    }
}
```
