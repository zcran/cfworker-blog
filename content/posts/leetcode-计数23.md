---
title: "leetcode-计数23"
date: 2026-08-01T10:40:52+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 上升下降字符串

给你一个字符串 s ，请你根据下面的算法重新构造字符串：

1. 从 s 中选出 最小 的字符，将它 接在 结果字符串的后面。
2. 从 s 剩余字符中选出比上一个添加字符更大的 最小 字符，将它 接在 结果字符串后面。
3. 重复步骤 2 ，直到你没法从 s 中选择字符。
4. 从 s 中选出 最大 的字符，将它 接在 结果字符串的后面。
5. 从 s 剩余字符中选出比上一个添加字符更小的 最大 字符，将它 接在 结果字符串后面。
6. 重复步骤 5 ，直到你没法从 s 中选择字符。
7. 重复步骤 1 到 6 ，直到 s 中所有字符都已经被选过。

在任何一步中，如果最小或者最大字符不止一个 ，你可以选择其中任意一个，并将其添加到结果字符串。

请你返回将 s 中字符重新排序后的 结果字符串 。


```
impl Solution {
    pub fn sort_string(s: String) -> String {
        // 统计每个字母的出现次数（只包含小写字母）
        let mut count = [0; 26];
        for ch in s.bytes() {
            count[(ch - b'a') as usize] += 1;
        }

        let total_len = s.len();
        let mut result = String::with_capacity(total_len);

        while result.len() < total_len {
            // 正向遍历：从小到大收集字符
            for i in 0..26 {
                if count[i] > 0 {
                    result.push((i as u8 + b'a') as char);
                    count[i] -= 1;
                }
            }

            // 反向遍历：从大到小收集字符
            for i in (0..26).rev() {
                if count[i] > 0 {
                    result.push((i as u8 + b'a') as char);
                    count[i] -= 1;
                }
            }
        }

        result
    }
}
```
