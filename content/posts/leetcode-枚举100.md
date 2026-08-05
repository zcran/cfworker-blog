---
title: "leetcode-枚举100"
date: 2026-07-09T10:05:04+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 可由多种立方和构造的整数

给你一个整数 n。

当存在 至少 两组不同的整数对 (a, b) 满足以下条件时，整数 x 被称为 好整数：

a 和 b 是正整数。
a <= b
x = a^3 + b^3

返回一个数组，其中包含所有小于等于 n 的好整数，并按升序排序。


```
use std::sync::LazyLock;
use std::collections::HashMap;

impl Solution {
    pub fn find_good_integers(n: i32) -> Vec<i32> {
        // 使用 LazyLock 实现懒加载，线程安全且无需 unsafe
        static GOOD_INTS: LazyLock<Vec<i32>> = LazyLock::new(|| {
            let max_val = 1_000_000_000;
            let mut cnt = HashMap::new();

            // 枚举所有 a^3 + b^3 <= MAX 的组合
            for a in 1.. {
                let a3 = a * a * a;
                if a3 > max_val / 2 {
                    break;
                }
                for b in a.. {
                    let sum = a3 + b * b * b;
                    if sum > max_val {
                        break;
                    }
                    *cnt.entry(sum).or_insert(0) += 1;
                }
            }

            // 收集出现次数 > 1 的 x 并排序
            let mut good: Vec<_> = cnt
                .into_iter()
                .filter_map(|(x, c)| if c > 1 { Some(x) } else { None })
                .collect();
            good.sort_unstable();
            good
        });

        // 二分查找 <= n 的边界
        let end = GOOD_INTS.partition_point(|&x| x <= n);
        GOOD_INTS[..end].to_vec()
    }
}
```
