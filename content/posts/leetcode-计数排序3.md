---
title: "Leetcode 计数排序3"
date: 2023-12-19T15:45:23+08:00
tags: ["leetcode", "计数排序"]
draft: false
---

## 数组的相对排序

给你两个数组，arr1 和 arr2，arr2 中的元素各不相同，arr2 中的每个元素都出现在 arr1 中。

对 arr1 中的元素进行排序，使 arr1 中项的相对顺序和 arr2 中的相对顺序相同。未在 arr2 中出现过的元素需要按照升序放在 arr1 的末尾。

```
use std::collections::HashMap;
use std::collections::HashSet;

impl Solution {
    pub fn relative_sort_array(arr1: Vec<i32>, arr2: Vec<i32>) -> Vec<i32> {
        let mut map = HashMap::new();
        let mut set = HashSet::new();
        let mut rv = Vec::new();
        let mut post = Vec::new();
        for a in &arr2 {
            set.insert(a);
        }
        for a in arr1 {
            if let Some(x) = map.get_mut(&a) {
                *x = *x + 1;
            } else {
                map.insert(a, 1);
            }
            if !set.contains(&a) {
                post.push(a);
            }
        }
        for a in &arr2 {
            if let Some(x) = map.get(&a) {
                for i in 0..(*x) {
                    rv.push(*a);
                }
            }
        }
        post.sort();
        rv.append(&mut post);
        rv
    }

}
```