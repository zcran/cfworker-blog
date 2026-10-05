---
title: "leetcode-双指针29"
date: 2026-09-05T09:53:02+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 供暖器

冬季已经来临。 你的任务是设计一个有固定加热半径的供暖器向所有房屋供暖。

在加热器的加热半径范围内的每个房屋都可以获得供暖。

现在，给出位于一条水平线上的房屋 houses 和供暖器 heaters 的位置，请你找出并返回可以覆盖所有房屋的最小加热半径。

注意：所有供暖器 heaters 都遵循你的半径标准，加热的半径也一样。


```
impl Solution {
    pub fn find_radius(mut houses: Vec<i32>, mut heaters: Vec<i32>) -> i32 {
        houses.sort_unstable();
        heaters.sort_unstable();

        let mut radius = 0;
        let mut hi = 0; // 当前最合适的供暖器索引

        for &house in &houses {
            // 找到离当前房屋最近的供暖器
            while hi + 1 < heaters.len()
                && (house - heaters[hi]).abs() >= (house - heaters[hi + 1]).abs()
            {
                hi += 1;
            }
            // 更新全局最小半径
            radius = radius.max((house - heaters[hi]).abs());
        }

        radius
    }
}
```
