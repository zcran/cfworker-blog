---
title: "leetcode-排序85"
date: 2026-10-05T00:35:05+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 数组的相对排序

给定两个数组，arr1 和 arr2，

arr2 中的元素各不相同
arr2 中的每个元素都出现在 arr1 中

对 arr1 中的元素进行排序，使 arr1 中项的相对顺序和 arr2 中的相对顺序相同。未在 arr2 中出现过的元素需要按照升序放在 arr1 的末尾。



```
use std::collections::HashMap;

impl Solution {
    pub fn relative_sort_array(arr1: Vec<i32>, arr2: Vec<i32>) -> Vec<i32> {
        // 记录 arr2 中每个元素的相对顺序
        let rank: HashMap<i32, i32> = arr2
            .into_iter()
            .enumerate()
            .map(|(i, v)| (v, i as i32))
            .collect();

        // 排序键：
        //   - 若元素出现在 arr2 中，用其在 arr2 中的下标作为排序依据；
        //   - 否则放到最后，并按元素值升序排列。
        let n = rank.len() as i32;
        let mut arr1 = arr1;
        arr1.sort_unstable_by_key(|&x| rank.get(&x).copied().unwrap_or(n + x));
        arr1
    }
}
```
