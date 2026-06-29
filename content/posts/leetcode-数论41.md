---
title: "leetcode-数论41"
date: 2026-06-20T11:09:56+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 统计美丽子字符串 I

给你一个字符串 s 和一个正整数 k 。

用 vowels 和 consonants 分别表示字符串中元音字母和辅音字母的数量。

如果某个字符串满足以下条件，则称其为 美丽字符串 ：

vowels == consonants，即元音字母和辅音字母的数量相等。
(vowels * consonants) % k == 0，即元音字母和辅音字母的数量的乘积能被 k 整除。
返回字符串 s 中 非空美丽子字符串 的数量。

子字符串是字符串中的一个连续字符序列。

英语中的 元音字母 为 'a'、'e'、'i'、'o' 和 'u' 。

英语中的 辅音字母 为除了元音字母之外的所有字母。


```
use std::collections::HashMap;

impl Solution {
    /// 计算美丽子字符串的数量
    ///
    /// 美丽条件：
    /// 1. 元音数 == 辅音数（即子串长度为偶数，且元音占一半）
    /// 2. 元音数 * 辅音数能被 k 整除（等价于 (len/2)² % k == 0）
    ///
    /// 优化策略：使用前缀和 + 哈希表，O(n) 时间复杂度
    pub fn beautiful_substrings(s: String, k: i32) -> i32 {
        let s = s.as_bytes();
        let n = s.len();
        let k = k as usize;

        // 预计算：每个位置是否为元音（1 表示元音，-1 表示辅音）
        // 前缀和 diff[i] = 元音数 - 辅音数
        // 当 diff[i] == diff[j] 时，子串 (j+1..i) 的元音数 == 辅音数
        let mut diff = 0;
        let mut ans = 0;

        // 哈希表：key = (diff, vowels_count mod k)，value = 出现次数
        // 需要记录元音数量，因为条件2 涉及元音数 * 辅音数
        // 当子串满足元音数 == 辅音数时，设元音数 = v，辅音数 = v
        // 条件2：v * v % k == 0
        let mut map = HashMap::new();
        // 初始状态：diff=0, vowels=0
        map.insert((0, 0), 1);

        let mut vowels = 0;

        for i in 0..n {
            let ch = s[i];
            let is_vowel = ch == b'a' || ch == b'e' || ch == b'i' || ch == b'o' || ch == b'u';

            if is_vowel {
                diff += 1;
                vowels += 1;
            } else {
                diff -= 1;
            }

            // 当前前缀的元音数量
            let v = vowels;

            // 检查所有可能的前缀 j，使得：
            // 1. diff[j] == diff[i]（元音数 == 辅音数）
            // 2. (v - vj)² % k == 0，其中 vj 是前缀 j 的元音数
            // 等价于：vj % k 满足 (v - vj)² % k == 0
            // 因为 v 和 vj 的范围都是 0..n，需要遍历所有可能的 vj mod k
            // 但实际上，当 diff 相同时，vj 的取值有限，可以通过哈希表优化

            // 遍历所有可能的 vj mod k，检查是否满足条件
            for r in 0..k {
                if (v - r) * (v - r) % k == 0 {
                    if let Some(&count) = map.get(&(diff, r)) {
                        ans += count;
                    }
                }
            }

            // 将当前状态加入哈希表
            *map.entry((diff, v % k)).or_insert(0) += 1;
        }

        ans
    }
}
```
