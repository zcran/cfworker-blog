---
title: "leetcode-枚举63"
date: 2026-07-09T10:05:02+08:00
tags: ["leetcode", "枚举"]
draft: false
---


##  三角形的最大高度


给你两个整数 red 和 blue，分别表示红色球和蓝色球的数量。你需要使用这些球来组成一个三角形，满足第 1 行有 1 个球，第 2 行有 2 个球，第 3 行有 3 个球，依此类推。

每一行的球必须是 相同 颜色，且相邻行的颜色必须 不同。

返回可以实现的三角形的 最大 高度。

```
impl Solution {
    pub fn max_height_of_triangle(red: i32, blue: i32) -> i32 {
        // 尝试两种排列方式：
        // 1. 第1行放红色，然后红蓝交替
        // 2. 第1行放蓝色，然后蓝红交替
        // 取两种方式的最大高度

        fn build_height(mut first_color: i32, mut second_color: i32) -> i32 {
            let mut row = 1;
            loop {
                // 奇数行用第一种颜色，偶数行用第二种颜色
                if row % 2 == 1 {
                    first_color -= row;
                    if first_color < 0 {
                        return row - 1;
                    }
                } else {
                    second_color -= row;
                    if second_color < 0 {
                        return row - 1;
                    }
                }
                row += 1;
            }
        }

        build_height(red, blue).max(build_height(blue, red))
    }
}
```
