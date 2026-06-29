---
title: "leetcode-单调栈3"
date: 2026-06-07T10:14:03+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 最大矩形


给定一个仅包含 0 和 1 、大小为 rows x cols 的二维二进制矩阵，找出只包含 1 的最大矩形，并返回其面积。


```
impl Solution {
    pub fn maximal_rectangle(matrix: Vec<Vec<char>>) -> i32 {
        let m = matrix.len();          // 矩阵的行数
        if m == 0 {
            return 0;                  // 空矩阵，没有矩形
        }
        let n = (&matrix[0]).len();    // 矩阵的列数
        if n == 0 {
            return 0;                  // 空矩阵，没有矩形
        }

        // 84 题的柱状图最大矩形算法（嵌套函数）
        fn largest_rectangle_area(heights: Vec<i32>) -> i32 {
            let mut heights = heights;
            // 在首尾各插入一个 0，作为哨兵
            heights.insert(0, 0);      // 左哨兵：保证栈永远不会空，简化边界处理
            heights.push(0);           // 右哨兵：强制最后清空栈，计算所有矩形

            // 单调递增栈（存储索引）
            let mut stack = vec![];
            let mut max_area = 0;

            // 遍历每个柱子（包括哨兵）
            for i in 0..heights.len() {
                // 当当前高度小于栈顶高度时，栈顶高度无法向右扩展
                // 计算以栈顶高度为高的矩形面积
                while !stack.is_empty() && heights[*stack.last().unwrap()] > heights[i] {
                    let h = heights[stack.pop().unwrap()];  // 弹出栈顶作为矩形的高度
                    // 计算宽度：右边界 i（不包含），左边界为新的栈顶（第一个比 h 小的位置）
                    let w = if stack.is_empty() {
                        i                               // 栈空时，左边界为 0
                    } else {
                        i - *stack.last().unwrap() - 1   // 左边界为栈顶索引 + 1
                    } as i32;
                    max_area = max_area.max(h * w);       // 更新最大面积
                }
                stack.push(i);      // 当前索引入栈
            }
            max_area
        }

        // 动态更新每一列的柱状图高度
        let mut heights = vec![0; n];    // 当前行的柱状图高度
        let mut max_area = -1;            // 全局最大矩形面积

        // 逐行处理，把每一行当作柱状图的底边
        for i in 0..m {
            for j in 0..n {
                // 更新第 j 列的柱子高度
                heights[j] = if matrix[i][j] == '1' {
                    heights[j] + 1        // 当前格子是 '1'，高度延续 +1
                } else {
                    0                     // 当前格子是 '0'，高度中断，重置为 0
                };
            }
            // 计算以当前行为底边的柱状图最大矩形面积，更新全局最大值
            max_area = max_area.max(largest_rectangle_area(heights.clone()));
        }

        return max_area;
    }
}
```
