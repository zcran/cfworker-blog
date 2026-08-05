---
title: "leetcode-枚举7"
date: 2026-07-09T10:04:59+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 顺次数

我们定义「顺次数」为：每一位上的数字都比前一位上的数字大 1 的整数。

请你返回由 [low, high] 范围内所有顺次数组成的 有序 列表（从小到大排序）。


```
impl Solution {
    /// 返回 [low, high] 范围内所有顺次数（每位数字比前一位大1），结果升序排列。
    pub fn sequential_digits(low: i32, high: i32) -> Vec<i32> {
        let mut result = Vec::new();

        // 枚举起始数字 1..=9
        for start in 1..=9 {
            let mut num = start;
            // 依次追加后续数字 start+1 ..= 9
            for next in (start + 1)..=9 {
                num = num * 10 + next;
                if num > high {
                    break; // 后续继续追加只会更大，提前终止
                }
                if num >= low {
                    result.push(num);
                }
            }
        }

        // 按起始数字枚举自然得到大致升序，但跨起始数字可能乱序，仍需排序
        result.sort_unstable(); // 使用更快的排序（不保证稳定，但这里不需要稳定）
        result
    }
}
```
