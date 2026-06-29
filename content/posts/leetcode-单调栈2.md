---
title: "leetcode-单调栈2"
date: 2026-06-07T10:14:03+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 柱状图中最大的矩形

给定 n 个非负整数，用来表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 1 。

求在该柱状图中，能够勾勒出来的矩形的最大面积。

```
impl Solution {
    pub fn largest_rectangle_area(mut heights: Vec<i32>) -> i32 {
        // 在末尾添加高度0，强制最后清空栈并计算所有剩余矩形
        heights.push(0);
        let mut max = 0;  // 记录最大矩形面积
        // 栈存储 (高度, 宽度)，宽度表示该高度可以向左扩展的距离
        let mut stack: Vec<(i32, i32)> = vec![];

        for height in heights {
            // 情况1：当前高度大于栈顶高度，直接入栈（宽度初始为1）
            if stack.is_empty() || stack.last().unwrap().0 < height {
                stack.push((height, 1));
                continue;
            }

            // 情况2：当前高度 <= 栈顶高度，需要弹出栈中所有高于当前高度的矩形
            while let Some((highest, width)) = stack.pop() {
                // 以弹出的高度为矩形高的最大面积
                max = max.max(highest * width);

                // 如果栈非空且新的栈顶高度仍大于当前高度
                if !stack.is_empty() && stack.last().unwrap().0 > height {
                    // 将弹出的宽度累加到新的栈顶上（向右扩展）
                    stack.last_mut().unwrap().1 += width;
                }
                else {
                    // 否则，将当前高度入栈，宽度 = 累加的所有弹出宽度 + 1
                    // 这个宽度表示当前高度可以向左延伸到所有弹出矩形的位置
                    stack.push((height, width + 1));
                    break;
                }
            }
        }
        return max;
    }
}
```
