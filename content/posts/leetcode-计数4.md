---
title: "leetcode-计数4"
date: 2026-08-01T10:40:50+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 赎金信

给你两个字符串：ransomNote 和 magazine ，判断 ransomNote 能不能由 magazine 里面的字符构成。

如果可以，返回 true ；否则返回 false 。

magazine 中的每个字符只能在 ransomNote 中使用一次。


```
impl Solution {
    /// 判断 ransomNote 是否能由 magazine 中的字符构成
    ///
    /// # 算法思路
    /// 统计 magazine 中每个字母的出现次数，然后逐一扣除 ransomNote 中字符的需求，
    /// 若任何字符需求超过 magazine 的供应，则无法构成。
    ///
    /// # 复杂度
    /// - 时间：O(n + m)，n 和 m 分别为两个字符串的长度
    /// - 空间：O(1)，仅使用固定大小的数组
    pub fn can_construct(ransom_note: String, magazine: String) -> bool {
        // 如果 ransomNote 更长，肯定无法构成（提前剪枝）
        if ransom_note.len() > magazine.len() {
            return false;
        }

        // 使用数组统计 magazine 中每个字母的出现次数（仅小写字母）
        let mut char_count = [0; 26];

        // 统计 magazine 中字符出现次数
        for ch in magazine.bytes() {
            char_count[(ch - b'a') as usize] += 1;
        }

        // 检查 ransomNote 中每个字符是否都能从 magazine 中获得
        for ch in ransom_note.bytes() {
            let idx = (ch - b'a') as usize;
            // 如果该字符数量不足，直接返回 false
            if char_count[idx] == 0 {
                return false;
            }
            // 消耗一个该字符
            char_count[idx] -= 1;
        }

        true
    }
}
```
