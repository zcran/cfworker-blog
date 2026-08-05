---
title: "leetcode-计数25"
date: 2026-08-01T10:40:52+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 统计最大组的数目

给定一个整数 n 。

我们需要根据数字的数位和将 1 到 n 的数字分组。例如，数字 14 和 5 属于 同一 组，而数字 13 和 3 属于 不同 组。

返回最大组的数字数量，即元素数量 最多 的组。


```
impl Solution {
    pub fn count_largest_group(n: i32) -> i32 {
        use std::collections::HashMap;

        // 数位和最大为 9 * 4 = 36（n <= 10^4）
        let mut groups = HashMap::new();
        let mut max_size = 0;

        for num in 1..=n {
            // 计算数位和
            let mut sum = 0;
            let mut x = num;
            while x > 0 {
                sum += x % 10;
                x /= 10;
            }

            // 加入对应组
            let size = groups.entry(sum).or_insert(0);
            *size += 1;
            max_size = max_size.max(*size);
        }

        // 统计最大组的数量
        groups.values().filter(|&&size| size == max_size).count() as i32
    }
}
```
