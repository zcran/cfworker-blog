---
title: "leetcode-贪心137"
date: 2026-10-03T12:55:43+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 重新分装苹果

给你一个长度为 n 的数组 apple 和另一个长度为 m 的数组 capacity 。

一共有 n 个包裹，其中第 i 个包裹中装着 apple[i] 个苹果。同时，还有 m 个箱子，第 i 个箱子的容量为 capacity[i] 个苹果。

请你选择一些箱子来将这 n 个包裹中的苹果重新分装到箱子中，返回你需要选择的箱子的 最小 数量。

注意，同一个包裹中的苹果可以分装到不同的箱子中。


```
impl Solution {
    pub fn minimum_boxes(apple: Vec<i32>, mut capacity: Vec<i32>) -> i32 {
        // 总苹果数固定，要使箱子数最少，优先选容量最大的箱子。
        let mut total: i32 = apple.iter().sum();
        capacity.sort_unstable_by(|a, b| b.cmp(a));

        let mut boxes = 0;
        for cap in capacity {
            if total <= 0 {
                break;
            }
            total -= cap;
            boxes += 1;
        }
        boxes
    }
}
```
