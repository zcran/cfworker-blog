---
title: "leetcode-数学20"
date: 2026-10-07T20:39:29+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 不浪费原料的汉堡制作方案

圣诞活动预热开始啦，汉堡店推出了全新的汉堡套餐。为了避免浪费原料，请你帮他们制定合适的制作计划。

给你两个整数 tomatoSlices 和 cheeseSlices，分别表示番茄片和奶酪片的数目。不同汉堡的原料搭配如下：

巨无霸汉堡：4 片番茄和 1 片奶酪
小皇堡：2 片番茄和 1 片奶酪

请你以 [total_jumbo, total_small]（[巨无霸汉堡总数，小皇堡总数]）的格式返回恰当的制作方案，使得剩下的番茄片 tomatoSlices 和奶酪片 cheeseSlices 的数量都是 0。

如果无法使剩下的番茄片 tomatoSlices 和奶酪片 cheeseSlices 的数量为 0，就请返回 []。


```
impl Solution {
    /// 计算巨无霸和小皇堡的制作数量，使番茄片和奶酪片恰好用完。
    ///
    /// 设巨无霸数量为 j，小皇堡数量为 s，则：
    ///   j + s = cheeseSlices
    ///   4j + 2s = tomatoSlices
    /// 消元得：2j = tomatoSlices - 2 * cheeseSlices，即
    ///   j = (tomatoSlices - 2 * cheeseSlices) / 2
    ///   s = cheeseSlices - j
    /// 需满足 j、s 均为非负整数。
    pub fn num_of_burgers(tomato_slices: i32, cheese_slices: i32) -> Vec<i32> {
        // 番茄片数必须为偶数，且 j 需落在 [0, cheese_slices] 内
        let diff = tomato_slices - 2 * cheese_slices;
        if tomato_slices % 2 != 0 || diff < 0 || diff > 2 * cheese_slices {
            return vec![];
        }

        let jumbo = diff / 2;
        vec![jumbo, cheese_slices - jumbo]
    }
}
```
