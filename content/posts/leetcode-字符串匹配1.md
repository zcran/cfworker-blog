---
title: "leetcode-字符串匹配1"
date: 2026-04-20T20:32:10+08:00
tags: ["leetcode", "字符串匹配"]
draft: false
---


## 找出字符串中第一个匹配项的下标


给你两个字符串 haystack 和 needle ，请你在 haystack 字符串中找出 needle 字符串的第一个匹配项的下标（下标从 0 开始）。如果 needle 不是 haystack 的一部分，则返回  -1 。


```
use std::collections::HashMap;

impl Solution {
    /// 在 haystack 中查找 needle 首次出现的位置。
    /// 若 needle 为空字符串，按约定返回 0。
    /// 若未找到，返回 -1。
    pub fn str_str(haystack: String, needle: String) -> i32 {
        // 转换为字节切片，后续比较直接基于 ASCII/UTF-8 字节进行。
        let haystack = haystack.as_bytes();
        let needle   = needle.as_bytes();

        let n = haystack.len();
        let m = needle.len();

        // 空模式串直接匹配在索引 0。
        if m == 0 {
            return 0;
        }

        // 模式串长度大于主串，不可能匹配。
        if n < m {
            return -1;
        }

        // ---------- 预处理：构建“坏字符位移表” ----------
        // Sunday 算法的核心：失配时检查主串中**当前对齐窗口后的第一个字符**。
        // 若该字符在模式串中出现，则滑动使得它们对齐；否则直接跳过整个模式串长度 +1。
        let shift = Self::build_shift_table(needle);

        // 当前对齐窗口的起始索引。
        let mut idx = 0;

        // 当剩余长度不足以容纳模式串时结束。
        while idx + m <= n {
            // 取当前窗口内的子串，与模式串进行字节级比较。
            let window = &haystack[idx..idx + m];
            if window == needle {
                return idx as i32;
            }

            // 边界检查：若已是最后一个可能位置，说明匹配失败。
            if idx + m >= n {
                break;
            }

            // 关键步骤：读取当前窗口**后一位**的字符（即 haystack[idx + m]）。
            let next_char = haystack[idx + m];

            // 从位移表中获取该字符对应的位移量。
            // 若该字符不在表中，则默认位移为 m + 1（即整个模式串长度再 +1）。
            let step = shift.get(&next_char).copied().unwrap_or(m + 1);

            // 窗口向前滑动。
            idx += step;
        }

        -1
    }

    /// 构建 Sunday 位移表（函数式风格，利用迭代器）。
    ///
    /// 对于模式串中出现的每个字符 c，记录它**到模式串末尾的距离**。
    /// 注意：若字符多次出现，仅保留最靠右的位置所对应的位移值。
    /// 位移值 = m - 该字符在模式串中最后一次出现的索引。
    ///
    /// 例如：needle = "hello", m = 5
    ///   'h': 5 - 0 = 5
    ///   'e': 5 - 1 = 4
    ///   'l': 5 - 3 = 2  （最后一次出现在索引 3）
    ///   'o': 5 - 4 = 1
    fn build_shift_table(needle: &[u8]) -> HashMap<u8, usize> {
        let m = needle.len();

        // 采用逆序遍历（从右向左），确保每个字符记录的是“最后出现位置”对应的位移。
        // 使用 fold 将迭代过程折叠成 HashMap。
        needle
            .iter()
            .enumerate()
            .rev()  // 逆序保证较左出现的字符会被较右出现的覆盖（HashMap 插入后值即最新）
            .fold(HashMap::new(), |mut acc, (i, &ch)| {
                // 只在首次遇到（即最靠右）时插入，因为逆序遍历。
                // entry API 保证只有未插入过的字符才会被记录。
                acc.entry(ch).or_insert(m - i);
                acc
            })
    }
}
```
