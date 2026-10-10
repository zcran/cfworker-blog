---
title: "leetcode-哈希表89"
date: 2026-10-09T20:08:01+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 栅栏的最宽宽度

给你一个整数数组 planks，其中 planks[i] 表示第 i 块木板的高度。每块木板的宽度为 1 个单位。

你想要用木板建造一个栅栏，栅栏中的所有木板必须具有 相同 的高度。

你可以直接使用原本的木板，或者将两块不同的原始木板组合成一块新木板，其高度 等于 这两块木板的高度之和。每块原始木板 最多 只能使用一次，并且不需要使用所有的原始木板。

返回可以建造的栅栏的 最大可能宽度。


```
use std::collections::HashMap;

impl Solution {
    pub fn maximum_width(planks: Vec<i32>) -> i32 {
        // 统计每种高度的木板数量
        let mut cnt: HashMap<i32, i32> = HashMap::new();
        for p in planks {
            *cnt.entry(p).or_insert(0) += 1;
        }

        // height -> 用该高度作为栅栏高度时能贡献的宽度
        let mut width: HashMap<i32, i32> = HashMap::new();

        // 单个木板直接使用：每种高度自身贡献其数量
        for (&h, &c) in &cnt {
            *width.entry(h).or_insert(0) += c;
        }

        // 组合两块不同高度的木板：对每一对不同高度求和，贡献 min(数量)
        let keys: Vec<i32> = cnt.keys().copied().collect();
        for i in 0..keys.len() {
            // 同高度两块拼接：高度 2h，贡献 c/2
            let h = keys[i];
            let c = cnt[&h];
            if c >= 2 {
                *width.entry(h * 2).or_insert(0) += c / 2;
            }
            // 不同高度拼接
            for j in (i + 1)..keys.len() {
                let h2 = keys[j];
                let sum = h + h2;
                let w = c.min(cnt[&h2]);
                *width.entry(sum).or_insert(0) += w;
            }
        }

        // 最大宽度即为所有候选高度中的最大值
        width.values().copied().max().unwrap_or(0)
    }
}
```
