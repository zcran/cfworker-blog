---
title: "leetcode-数论15"
date: 2026-06-20T11:09:55+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 可互换矩形的组数


用一个下标从 0 开始的二维整数数组 rectangles 来表示 n 个矩形，其中 rectangles[i] = [widthi, heighti] 表示第 i 个矩形的宽度和高度。

如果两个矩形 i 和 j（i < j）的宽高比相同，则认为这两个矩形 可互换 。更规范的说法是，两个矩形满足 widthi/heighti == widthj/heightj（使用实数除法而非整数除法），则认为这两个矩形 可互换 。

计算并返回 rectangles 中有多少对 可互换 矩形。

```
use std::collections::HashMap;

impl Solution {
    /// 计算矩形数组中宽高比相同的矩形对数
    ///
    /// # 思路
    /// 将每个矩形的宽高比化简为最简分数 (w/g, h/g)，使用哈希表统计每种比例的出现次数，
    /// 然后对每种比例计算组合数 C(n, 2) = n*(n-1)/2 并累加。
    pub fn interchangeable_rectangles(rectangles: Vec<Vec<i32>>) -> i64 {
        let mut count: HashMap<(i32, i32), i64> = HashMap::new();

        for rect in rectangles {
            let g = gcd(rect[0], rect[1]);
            let ratio = (rect[0] / g, rect[1] / g);
            *count.entry(ratio).or_insert(0) += 1;
        }

        count
            .values()
            .map(|&n| n * (n - 1) / 2)
            .sum()
    }
}

/// 计算两个数的最大公约数（欧几里得算法）
#[inline]
fn gcd(mut a: i32, mut b: i32) -> i32 {
    while b != 0 {
        let temp = b;
        b = a % b;
        a = temp;
    }
    a.abs()
}
```
