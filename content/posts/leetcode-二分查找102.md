---
title: "leetcode-二分查找102"
date: 2026-09-28T12:29:17+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 交换和

给定两个整数数组，请交换一对数值（每个数组中取一个数值），使得两个数组所有元素的和相等。

返回一个数组，第一个元素是第一个数组中要交换的元素，第二个元素是第二个数组中要交换的元素。若有多个答案，返回任意一个均可。若无满足条件的数值，返回空数组。


```
impl Solution {
    pub fn find_swap_values(array1: Vec<i32>, array2: Vec<i32>) -> Vec<i32> {
        use std::collections::HashSet;

        let diff = (array1.iter().sum::<i32>() - array2.iter().sum::<i32>()) / 2;
        // 差为奇数时无法通过交换使总和相等
        if (array1.iter().sum::<i32>() - array2.iter().sum::<i32>()) % 2 != 0 {
            return vec![];
        }

        let set: HashSet<i32> = array2.into_iter().collect();
        // 需要 a - b = diff，即 b = a - diff
        array1
            .into_iter()
            .find(|&a| set.contains(&(a - diff)))
            .map(|a| vec![a, a - diff])
            .unwrap_or_default()
    }
}
```
