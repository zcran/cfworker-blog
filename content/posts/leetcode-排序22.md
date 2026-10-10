---
title: "leetcode-排序22"
date: 2026-10-05T00:35:00+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 数组的相对排序

给你两个数组，arr1 和 arr2，arr2 中的元素各不相同，arr2 中的每个元素都出现在 arr1 中。

对 arr1 中的元素进行排序，使 arr1 中项的相对顺序和 arr2 中的相对顺序相同。未在 arr2 中出现过的元素需要按照升序放在 arr1 的末尾。


```
use std::collections::HashMap;

impl Solution {
    /// 按 arr2 的相对顺序排列 arr1，未出现的元素升序置于末尾
    ///
    /// 思路：arr2 元素互异，只需一份「值 -> 在 arr2 中的次序」映射，
    /// 无需额外 HashSet。用计数法处理：
    ///   1. 统计 arr1 各值频次（HashMap 同时承担查表与计数）；
    ///   2. 按 arr2 顺序整段输出命中值并从表中移除；
    ///   3. 表中剩余的值即未在 arr2 出现的元素，按键升序逐个按频次输出。
    /// 时间 O(n + m + V log V)，额外空间 O(n + m)。
    pub fn relative_sort_array(arr1: Vec<i32>, arr2: Vec<i32>) -> Vec<i32> {
        // 统计 arr1 中各值出现次数
        let mut cnt: HashMap<i32, i32> = HashMap::with_capacity(arr1.len());
        for x in &arr1 {
            *cnt.entry(*x).or_insert(0) += 1;
        }

        let mut ans = Vec::with_capacity(arr1.len());

        // 按 arr2 顺序整段输出
        for &x in &arr2 {
            if let Some(c) = cnt.remove(&x) {
                ans.extend(std::iter::repeat(x).take(c as usize));
            }
        }

        // 未在 arr2 出现的元素按键升序输出
        let mut rest: Vec<(i32, i32)> = cnt.into_iter().collect();
        rest.sort_unstable_by_key(|&(v, _)| v);
        for (v, c) in rest {
            ans.extend(std::iter::repeat(v).take(c as usize));
        }

        ans
    }
}
```
