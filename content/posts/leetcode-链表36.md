---
title: "leetcode-链表36"
date: 2026-06-29T10:55:35+08:00
tags: ["leetcode", "链表"]
draft: false
---


## 螺旋矩阵 IV

给你两个整数：m 和 n ，表示矩阵的维数。

另给你一个整数链表的头节点 head 。

请你生成一个大小为 m x n 的螺旋矩阵，矩阵包含链表中的所有整数。链表中的整数从矩阵 左上角 开始、顺时针 按 螺旋 顺序填充。如果还存在剩余的空格，则用 -1 填充。

返回生成的矩阵。


```
impl Solution {
    pub fn spiral_matrix(m: i32, n: i32, mut head: Option<Box<ListNode>>) -> Vec<Vec<i32>> {
        let (m, n) = (m as usize, n as usize);
        let mut result = vec![vec![-1; n]; m];

        // 定义四个边界：上、下、左、右
        let (mut top, mut bottom, mut left, mut right) = (0, m - 1, 0, n - 1);
        // 方向控制：0=右, 1=下, 2=左, 3=上
        let mut dir = 0;
        let (mut row, mut col) = (0, 0);

        while let Some(node) = head {
            // 填充当前位置
            result[row][col] = node.val;

            // 根据方向计算下一个位置
            match dir {
                0 => { // 向右
                    if col == right {
                        top += 1;
                        dir = 1;
                        row += 1;
                    } else {
                        col += 1;
                    }
                }
                1 => { // 向下
                    if row == bottom {
                        right -= 1;
                        dir = 2;
                        col -= 1;
                    } else {
                        row += 1;
                    }
                }
                2 => { // 向左
                    if col == left {
                        bottom -= 1;
                        dir = 3;
                        row -= 1;
                    } else {
                        col -= 1;
                    }
                }
                3 => { // 向上
                    if row == top {
                        left += 1;
                        dir = 0;
                        col += 1;
                    } else {
                        row -= 1;
                    }
                }
                _ => unreachable!(),
            }

            head = node.next;
        }

        result
    }
}
```
