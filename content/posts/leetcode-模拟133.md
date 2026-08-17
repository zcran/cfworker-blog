---
title: "leetcode-模拟133"
date: 2026-08-08T11:31:16+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 元音辅音得分

给你一个字符串 s，由小写英文字母、空格和数字组成。

令 v 表示 s 中元音字母的数量，c 表示辅音字母的数量。

元音字母是 'a'、'e'、'i'、'o' 和 'u'，而英文字母表中除元音外的其他字母均视为辅音字母。

字符串 s 的 得分 定义如下：

如果 c > 0，则 score = floor(v / c)，其中 floor 表示 向下取整 到最接近的整数。

否则，如果 c = 0，则 score = 0。

返回一个整数，表示字符串的得分。


```
impl Solution {
    /// 计算字符串得分：元音数 / 辅音数（向下取整）
    ///
    /// 优化点：
    /// - 位掩码 O(1) 判断元音，替代字符串查找
    /// - as_bytes() 避免 UTF-8 边界检查开销
    /// - 单次遍历，零额外内存分配
    pub fn vowel_consonant_score(s: String) -> i32 {
        let mut vowels = 0i32;
        let mut consonants = 0i32;

        for &b in s.as_bytes() {
            // 快速过滤：只处理小写字母 ASCII 97-122
            if b < b'a' || b > b'z' {
                continue;
            }
            // 位掩码：a(0), e(4), i(8), o(14), u(20)
            const MASK: u32 = (1 << 0) | (1 << 4) | (1 << 8) | (1 << 14) | (1 << 20);
            if (MASK >> (b - b'a')) & 1 == 1 {
                vowels += 1;
            } else {
                consonants += 1;
            }
        }

        if consonants > 0 {
            vowels / consonants
        } else {
            0
        }
    }
}
```
