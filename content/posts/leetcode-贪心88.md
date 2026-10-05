---
title: "leetcode-贪心88"
date: 2026-10-03T12:55:40+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 两栋颜色不同且距离最远的房子

街上有 n 栋房子整齐地排成一列，每栋房子都粉刷上了漂亮的颜色。给你一个下标从 0 开始且长度为 n 的整数数组 colors ，其中 colors[i] 表示第  i 栋房子的颜色。

返回 两栋 颜色 不同 房子之间的 最大 距离。

第 i 栋房子和第 j 栋房子之间的距离是 abs(i - j) ，其中 abs(x) 是 x 的绝对值。


```
impl Solution {
    pub fn max_distance(colors: Vec<i32>) -> i32 {
        let n = colors.len();
        let first = colors[0];
        let last = colors[n - 1];

        // 最大距离必然涉及首尾之一：从右往左找第一个与 colors[0] 不同的房子
        let mut max = 0;
        for i in (0..n).rev() {
            if colors[i] != first {
                max = max.max(i as i32);
                break;
            }
        }
        // 从左往右找第一个与 colors[n-1] 不同的房子
        for i in 0..n {
            if colors[i] != last {
                max = max.max((n - 1 - i) as i32);
                break;
            }
        }
        max
    }
}
```
