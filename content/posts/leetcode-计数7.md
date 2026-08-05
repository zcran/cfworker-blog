---
title: "leetcode-计数7"
date: 2026-08-01T10:40:51+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 重构字符串

给定一个字符串 s ，检查是否能重新排布其中的字母，使得两相邻的字符不同。

返回 s 的任意可能的重新排列。若不可行，返回空字符串 "" 。


```
use std::collections::HashMap;

impl Solution {
    /// 重新排列字符串，使得相邻字符不同
    ///
    /// # 算法思路
    /// 1. 统计每个字符的出现次数
    /// 2. 如果某个字符出现次数 > (n + 1) / 2，无法满足条件，返回空字符串
    /// 3. 使用**间隔填充法**：先填充偶数位（0, 2, 4...），再填充奇数位（1, 3, 5...）
    ///    这样可以确保相同字符不会相邻
    ///
    /// # 复杂度
    /// - 时间：O(n + m log m)，m 为不同字符数（最多 26）
    /// - 空间：O(n)，用于存储结果字符串
    pub fn reorganize_string(s: String) -> String {
        let n = s.len();
        if n <= 1 {
            return s;
        }

        // 1. 统计字符频率
        let mut freq = HashMap::with_capacity(26);
        for ch in s.bytes() {
            *freq.entry(ch).or_insert(0) += 1;
        }

        // 2. 找出最高频率的字符，判断可行性
        let &max_freq = freq.values().max().unwrap();
        // 如果最高频率 > (n + 1) / 2，无法使相邻字符不同
        if max_freq > (n + 1) / 2 {
            return String::new();
        }

        // 3. 按频率降序排序
        let mut chars: Vec<_> = freq.into_iter().collect();
        chars.sort_unstable_by(|a, b| b.1.cmp(&a.1));

        // 4. 间隔填充：先填偶数索引，再填奇数索引
        let mut result = vec![0; n];
        let mut idx = 0;

        for (ch, count) in chars {
            for _ in 0..count {
                result[idx] = ch;
                idx += 2;
                // 如果偶数位填完，跳到奇数位
                if idx >= n {
                    idx = 1;
                }
            }
        }

        // 安全转换：result 中只包含有效 ASCII 字符
        unsafe { String::from_utf8_unchecked(result) }
    }
}
```
