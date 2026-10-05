---
title: "leetcode-矩阵27"
date: 2026-09-19T10:41:33+08:00
tags: ["leetcode", "矩阵"]
draft: false
---


## 统计有序矩阵中的负数

给你一个 m * n 的矩阵 grid，矩阵中的元素无论是按行还是按列，都以非严格递减顺序排列。 请你统计并返回 grid 中 负数 的数目。


```
impl Solution {
    pub fn count_negatives(grid: Vec<Vec<i32>>) -> i32 {
        let cols = grid[0].len();
        let mut count = 0;
        // 从右上角开始：每行负数都集中在右侧连续一段
        // 利用"行和列均非严格递减"的单调性，向下移动时列索引只会减小
        let mut j = cols as i32 - 1;
        for row in &grid {
            // 找到当前行最左边负数的位置，右边全是负数
            while j >= 0 && row[j as usize] < 0 {
                j -= 1;
            }
            // 当前行负数个数 = cols - 1 - j
            count += cols as i32 - 1 - j;
        }
        count
    }
}
```
