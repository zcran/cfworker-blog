---
title: "leetcode-字符串匹配20"
date: 2026-04-20T20:32:10+08:00
tags: ["leetcode", "字符串匹配"]
draft: false
---


## 多次搜索

给定一个较长字符串big和一个包含较短字符串的数组smalls，设计一个方法，根据smalls中的每一个较短字符串，对big进行搜索。输出smalls中的字符串在big里出现的所有位置positions，其中positions[i]为smalls[i]出现的所有位置。

```
use std::iter::FromIterator;

impl Solution {
    /// 主函数：对每个小字符串，返回它在 big 中出现位置的有序列表。
    pub fn multi_search(big: String, smalls: Vec<String>) -> Vec<Vec<i32>> {
        // 将 big 转换为字节数组以便高效处理
        let big_bytes = big.as_bytes();

        // 使用迭代器对每个 small 执行搜索，收集结果
        smalls
            .into_iter()
            .map(|word| Self::find_all_occurrences(big_bytes, word.as_bytes()))
            .collect()
    }

    /// 在文本 text 中查找模式 pattern 的所有出现起始位置（字节索引）。
    /// 使用 KMP 算法，时间复杂度 O(|text| + |pattern|)。
    /// 返回有序的 i32 列表。
    fn find_all_occurrences(text: &[u8], pattern: &[u8]) -> Vec<i32> {
        let n = text.len();
        let m = pattern.len();

        // 边界条件：空模式或模式比文本长，均无匹配
        if m == 0 || m > n {
            return Vec::new();
        }

        // ---------- 构建 KMP 前缀函数 π ----------
        // π[i] 表示 pattern[..=i] 的最长相等真前缀与真后缀的长度
        let mut pi = vec![0; m];
        let mut j = 0;
        for i in 1..m {
            // 当不匹配时，回退到前一个前缀位置
            while j > 0 && pattern[i] != pattern[j] {
                j = pi[j - 1];
            }
            if pattern[i] == pattern[j] {
                j += 1;
            }
            pi[i] = j;
        }

        // ---------- 在 text 中搜索 pattern ----------
        let mut res = Vec::new();
        j = 0;
        for (i, &ch) in text.iter().enumerate() {
            // 字符不匹配时，根据前缀函数回退
            while j > 0 && ch != pattern[j] {
                j = pi[j - 1];
            }
            if ch == pattern[j] {
                j += 1;
            }
            // 完全匹配 pattern
            if j == m {
                // 计算起始索引（i 是当前字符的索引，需转换为起始位置）
                let start = (i + 1 - m) as i32;
                res.push(start);
                // 继续查找下一个可能的匹配（允许重叠）
                j = pi[j - 1];
            }
        }

        res
    }
}
```
