---
title: "leetcode-哈希表76"
date: 2026-10-09T20:08:00+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 将元素分配给有约束条件的组

给你一个整数数组 groups，其中 groups[i] 表示第 i 组的大小。另给你一个整数数组 elements。

请你根据以下规则为每个组分配 一个 元素：

如果 groups[i] 能被 elements[j] 整除，则下标为 j 的元素可以分配给组 i。
如果有多个元素满足条件，则分配 最小的下标 j 的元素。
如果没有元素满足条件，则分配 -1 。

返回一个整数数组 assigned，其中 assigned[i] 是分配给组 i 的元素的索引，若无合适的元素，则为 -1。

注意：一个元素可以分配给多个组。


```
impl Solution {
    pub fn assign_elements(groups: Vec<i32>, elements: Vec<i32>) -> Vec<i32> {
        // 只需处理不超过 groups 最大值的元素/倍数
        let max_g = *groups.iter().max().unwrap() as usize;

        // target[v] = 能整除 v 的最小元素下标，-1 表示暂无
        let mut target = vec![-1i32; max_g + 1];

        for (i, &x) in elements.iter().enumerate() {
            let x = x as usize;
            // 超出范围，或该值已更早被标记过（其倍数也必然已标记），跳过
            if x > max_g || target[x] >= 0 {
                continue;
            }
            // 枚举 x 的倍数，未被标记的记下当前下标
            let mut y = x;
            while y <= max_g {
                if target[y] < 0 {
                    target[y] = i as i32;
                }
                y += x;
            }
        }

        // 按 groups 中每个值查询答案
        groups
            .into_iter()
            .map(|g| target[g as usize])
            .collect()
    }
}
```
