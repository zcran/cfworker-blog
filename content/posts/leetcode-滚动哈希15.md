---
title: "leetcode-滚动哈希15"
date: 2026-04-17T20:00:31+08:00
tags: ["leetcode", "滚动哈希"]
draft: false
---


## 找出数组中的美丽下标 I

给你一个下标从 0 开始的字符串 s 、字符串 a 、字符串 b 和一个整数 k 。

如果下标 i 满足以下条件，则认为它是一个 美丽下标：

0 <= i <= s.length - a.length
s[i..(i + a.length - 1)] == a
存在下标 j 使得：
0 <= j <= s.length - b.length
s[j..(j + b.length - 1)] == b
|j - i| <= k
以数组形式按 从小到大排序 返回美丽下标。

```
impl Solution {
    /// 找到所有下标 i，使得 s[i..] 以字符串 a 开头，且存在某个 j 使得 s[j..] 以 b 开头，且 |i - j| <= k。
    /// 返回所有满足条件的 i（升序）。
    pub fn beautiful_indices(s: String, a: String, b: String, k: i32) -> Vec<i32> {
        // 转为字节数组，提高效率（假设输入只包含 ASCII 字符）
        let s_bytes = s.as_bytes();
        let a_bytes = a.as_bytes();
        let b_bytes = b.as_bytes();

        // 使用 KMP 找出所有匹配位置
        let pos_a = Self::kmp_all(s_bytes, a_bytes);
        let pos_b = Self::kmp_all(s_bytes, b_bytes);

        // 如果任意模式未出现，直接返回空
        if pos_a.is_empty() || pos_b.is_empty() {
            return vec![];
        }

        // 对 pos_b 排序（KMP 已保证升序，此处显式排序确保二分正确）
        let pos_b = pos_b; // 已是升序

        // 使用函数式风格：对每个 a 的位置，检查是否存在 b 的位置距离 <= k
        pos_a
            .into_iter()
            .filter(|&i| {
                // 在 pos_b 中找到第一个 >= i 的位置
                match pos_b.binary_search(&i) {
                    Ok(_) => true, // 相等，距离 0 <= k
                    Err(idx) => {
                        // idx 是第一个大于 i 的位置，检查前一个和后一个
                        let left_ok = idx > 0 && i - pos_b[idx - 1] <= k as usize;
                        let right_ok = idx < pos_b.len() && pos_b[idx] - i <= k as usize;
                        left_ok || right_ok
                    }
                }
            })
            .map(|i| i as i32)
            .collect()
    }

    /// KMP 算法，返回模式串 pattern 在文本 text 中所有匹配的起始索引（升序）。
    fn kmp_all(text: &[u8], pattern: &[u8]) -> Vec<usize> {
        if pattern.is_empty() {
            return (0..=text.len()).collect();
        }

        // 1. 构建 next 数组（前缀函数）
        let next = Self::build_next(pattern);

        // 2. 搜索匹配位置
        let mut matches = Vec::new();
        let mut j = 0; // 当前已匹配的模式长度
        for (i, &ch) in text.iter().enumerate() {
            // 不匹配时回退
            while j > 0 && ch != pattern[j] {
                j = next[j - 1];
            }
            if ch == pattern[j] {
                j += 1;
            }
            if j == pattern.len() {
                // 找到一个匹配，起始索引为 i - j + 1
                matches.push(i + 1 - j);
                j = next[j - 1]; // 继续寻找下一个重叠匹配
            }
        }
        matches
    }

    /// 构建 KMP 的 next 数组（前缀函数），next[i] 表示 pattern[0..=i] 的最长相等真前后缀的长度。
    fn build_next(pattern: &[u8]) -> Vec<usize> {
        let mut next = vec![0; pattern.len()];
        let mut j = 0;
        for i in 1..pattern.len() {
            while j > 0 && pattern[i] != pattern[j] {
                j = next[j - 1];
            }
            if pattern[i] == pattern[j] {
                j += 1;
            }
            next[i] = j;
        }
        next
    }
}
```
