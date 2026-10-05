---
title: "leetcode-贪心85"
date: 2026-10-03T12:55:39+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 从双倍数组中还原原数组

一个整数数组 original 可以转变成一个 双倍 数组 changed ，转变方式为将 original 中每个元素 值乘以 2 加入数组中，然后将所有元素 随机打乱 。

给你一个数组 changed ，如果 change 是 双倍 数组，那么请你返回 original数组，否则请返回空数组。original 的元素可以以 任意 顺序返回。


```
use std::collections::HashMap;

impl Solution {
    pub fn find_original_array(mut changed: Vec<i32>) -> Vec<i32> {
        // 奇数个元素不可能由「每个原元素配对一个双倍元素」组成
        if changed.len() % 2 != 0 {
            return Vec::new();
        }

        // 升序排序，保证先处理较小的数，避免大数先被配对
        changed.sort_unstable();

        // 统计每个数值的出现次数
        let mut count: HashMap<i32, i32> = HashMap::new();
        for &x in &changed {
            *count.entry(x).or_insert(0) += 1;
        }

        let mut original = Vec::with_capacity(changed.len() / 2);
        for &x in &changed {
            let cx = count.get(&x).copied().unwrap_or(0);
            if cx == 0 {
                continue; // 已被配对消耗
            }

            let c2x = count.get(&(x * 2)).copied().unwrap_or(0);
            // 需要 x 和 2x 都还有剩余；注意 x == 0 时 2x 也是 0，需 cx >= 2
            if c2x == 0 || (x == 0 && cx < 2) {
                return Vec::new();
            }

            *count.get_mut(&x).unwrap() -= 1;
            *count.get_mut(&(x * 2)).unwrap() -= 1;
            original.push(x);
        }
        original
    }
}
```
