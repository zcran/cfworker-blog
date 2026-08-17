---
title: "leetcode-模拟71"
date: 2026-08-08T11:31:12+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 替换数组中的元素

给你一个下标从 0 开始的数组 nums ，它包含 n 个 互不相同 的正整数。请你对这个数组执行 m 个操作，在第 i 个操作中，你需要将数字 operations[i][0] 替换成 operations[i][1] 。

题目保证在第 i 个操作中：

operations[i][0] 在 nums 中存在。
operations[i][1] 在 nums 中不存在。

请你返回执行完所有操作后的数组。


```
use std::collections::HashMap;

impl Solution {
    /// 逆向处理操作链：从后往前遍历，将每个 from 映射到其最终值。
    /// 若 to 已被映射，则 from 直接指向 to 的最终目标，避免中间跳转。
    pub fn array_change(mut nums: Vec<i32>, operations: Vec<Vec<i32>>) -> Vec<i32> {
        let mut map: HashMap<i32, i32> = HashMap::with_capacity(operations.len());
        for op in operations.iter().rev() {
            let to = map.get(&op[1]).copied().unwrap_or(op[1]);
            map.insert(op[0], to);
        }
        for x in &mut nums {
            if let Some(&v) = map.get(x) {
                *x = v;
            }
        }
        nums
    }
}
```
