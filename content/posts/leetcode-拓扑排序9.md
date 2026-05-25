---
title: "leetcode-拓扑排序9"
date: 2026-04-26T21:00:34+08:00
tags: ["leetcode", "拓扑排序"]
draft: false
---


## 奇怪的打印机 II

给你一个奇怪的打印机，它有如下两个特殊的打印规则：

·每一次操作时，打印机会用同一种颜色打印一个矩形的形状，每次打印会覆盖矩形对应格子里原本的颜色。
·一旦矩形根据上面的规则使用了一种颜色，那么 相同的颜色不能再被使用 。

给你一个初始没有颜色的 m x n 的矩形 targetGrid ，其中 targetGrid[row][col] 是位置 (row, col) 的颜色。

如果你能按照上述规则打印出矩形 targetGrid ，请你返回 true ，否则返回 false 。

```
use std::collections::HashMap;

/// 表示一种颜色的最小包围矩形（边界由该颜色出现的最左/最右/最上/最下位置决定）
#[derive(Debug, Clone)]
struct BoundingRect {
    min_row: usize,
    max_row: usize,
    min_col: usize,
    max_col: usize,
}

impl BoundingRect {
    /// 创建新的矩形，初始化位置为单个单元格
    fn new(row: usize, col: usize) -> Self {
        BoundingRect {
            min_row: row,
            max_row: row,
            min_col: col,
            max_col: col,
        }
    }

    /// 扩展矩形边界以包含新点 (row, col)
    fn expand(&mut self, row: usize, col: usize) {
        self.min_row = self.min_row.min(row);
        self.max_row = self.max_row.max(row);
        self.min_col = self.min_col.min(col);
        self.max_col = self.max_col.max(col);
    }
}

/// 用于解决「打印网格」问题的核心结构。
/// 算法思路：逆向思考 —— 从最终网格反向擦除颜色。
/// 每次选择一个颜色，如果它的包围矩形内只有该颜色或已经擦除的空白 (0)，
/// 就可以将该矩形全部擦除（设为 0），并删除该颜色。重复直到所有颜色擦除。
/// 如果某轮没有可擦除的颜色，说明无法通过合法打印顺序得到目标网格。
struct ColorPrinter {
    /// 当前网格状态（动态修改，擦除的颜色变为 0）
    grid: Vec<Vec<i32>>,
    rows: usize,
    cols: usize,
    /// 每种颜色对应的最小包围矩形
    color_rects: HashMap<i32, BoundingRect>,
}

impl ColorPrinter {
    /// 从目标网格创建打印机实例，并自动计算每个颜色的包围矩形。
    fn new(target_grid: Vec<Vec<i32>>) -> Self {
        let rows = target_grid.len();
        let cols = target_grid[0].len();
        let mut printer = ColorPrinter {
            grid: target_grid,
            rows,
            cols,
            color_rects: HashMap::new(),
        };
        printer.compute_bounding_rects();
        printer
    }

    /// 遍历整个网格，计算每个颜色出现的最小矩形区域。
    fn compute_bounding_rects(&mut self) {
        use std::collections::hash_map::Entry;
        for row in 0..self.rows {
            for col in 0..self.cols {
                let color = self.grid[row][col];
                match self.color_rects.entry(color) {
                    Entry::Occupied(mut entry) => entry.get_mut().expand(row, col),
                    Entry::Vacant(entry) => {
                        entry.insert(BoundingRect::new(row, col));
                    }
                }
            }
        }
    }

    /// 尝试擦除当前所有“可擦除”的颜色（矩形内只包含自身颜色或已擦除的 0）。
    /// 返回 `true` 如果至少擦除了一种颜色，否则 `false`。
    fn erase_removable_colors(&mut self) -> bool {
        // 找出所有满足条件的颜色
        let removable: Vec<i32> = self
            .color_rects
            .iter()
            .filter_map(|(color, rect)| {
                if self.rect_contains_only_color(rect, *color) {
                    Some(*color)
                } else {
                    None
                }
            })
            .collect();

        if removable.is_empty() {
            return false;
        }

        // 擦除这些颜色（将对应矩形区域置 0）
        for &color in &removable {
            let rect = &self.color_rects[&color];
            for row in rect.min_row..=rect.max_row {
                for col in rect.min_col..=rect.max_col {
                    self.grid[row][col] = 0;
                }
            }
            self.color_rects.remove(&color);
        }

        true
    }

    /// 检查一个包围矩形内部是否只包含指定的颜色或空白 (0)。
    fn rect_contains_only_color(&self, rect: &BoundingRect, color: i32) -> bool {
        (rect.min_row..=rect.max_row)
            .flat_map(|row| (rect.min_col..=rect.max_col).map(move |col| (row, col)))
            .all(|(row, col)| {
                let c = self.grid[row][col];
                c == 0 || c == color
            })
    }

    /// 判断整个网格是否能通过合法的矩形打印序列得到。
    /// 反复擦除可擦除颜色，直到全部擦除或无法继续。
    fn can_print(mut self) -> bool {
        while !self.color_rects.is_empty() {
            if !self.erase_removable_colors() {
                // 存在颜色无法被擦除，说明无法通过合法顺序打印
                return false;
            }
        }
        true
    }
}

impl Solution {
    /// 判断目标网格是否可以通过一系列矩形打印操作得到。
    /// 每次打印选择一个矩形区域并覆盖为一种颜色（后打印的覆盖先打印的）。
    ///
    /// 时间复杂度 O(C * (R*C))，其中 C 为颜色数，R*C 为网格面积。
    /// 空间复杂度 O(C)。
    pub fn is_printable(target_grid: Vec<Vec<i32>>) -> bool {
        let printer = ColorPrinter::new(target_grid);
        printer.can_print()
    }
}
```
