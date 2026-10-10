---
title: "leetcode-哈希表17"
date: 2026-10-09T20:07:56+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 数组的度

给定一个非空且只包含非负数的整数数组 nums，数组的 度 的定义是指数组里任一元素出现频数的最大值。

你的任务是在 nums 中找到与 nums 拥有相同大小的度的最短连续子数组，返回其长度。


```
use std::collections::HashMap;

impl Solution {
    pub fn find_shortest_sub_array(nums: Vec<i32>) -> i32 {
        // 对每个值记录 (出现次数, 首次出现下标)。
        // 度为该值的出现次数，最短子数组即从首次到当前下标的长度。
        // 遍历时动态更新最大度和最短长度。
        let mut map: HashMap<i32, (i32, usize)> = HashMap::new();
        let mut degree = 0;
        let mut ans = 0;

        for (i, &n) in nums.iter().enumerate() {
            let entry = map.entry(n).or_insert((0, i));
            entry.0 += 1;
            let span = i - entry.1 + 1;
            if entry.0 > degree {
                degree = entry.0;
                ans = span;
            } else if entry.0 == degree {
                ans = ans.min(span);
            }
        }

        ans as i32
    }
}
```
