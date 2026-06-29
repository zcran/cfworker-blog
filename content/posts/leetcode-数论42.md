---
title: "leetcode-数论42"
date: 2026-06-20T11:09:56+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 统计美丽子字符串 II

给你一个字符串 s 和一个正整数 k 。

用 vowels 和 consonants 分别表示字符串中元音字母和辅音字母的数量。

如果某个字符串满足以下条件，则称其为 美丽字符串 ：

· vowels == consonants，即元音字母和辅音字母的数量相等。

· (vowels * consonants) % k == 0，即元音字母和辅音字母的数量的乘积能被 k 整除。

返回字符串 s 中 非空美丽子字符串 的数量。

子字符串是字符串中的一个连续字符序列。

英语中的 元音字母 为 'a'、'e'、'i'、'o' 和 'u' 。

英语中的 辅音字母 为除了元音字母之外的所有字母。


```
impl Solution {
    /// 计算美丽子字符串的数量
    ///
    /// 核心优化：
    /// 1. 找到最小的 t，使得 t² % k == 0
    /// 2. 美丽子串的长度必为 2t 的倍数
    /// 3. 使用哈希表按 (位置 mod 2t, diff) 分组统计
    pub fn beautiful_substrings(s: String, k: i32) -> i64 {
        let s = s.as_bytes();

        // 找到最小的 t，使得 t² % k == 0
        let mut t = 1;
        while (t * t) % k != 0 {
            t += 1;
        }
        let period = (2 * t) as usize;

        // cnt[period][diff] 存储到达当前位置时，满足条件的子串数
        // 使用 Vec<HashMap> 按周期分组
        let mut cnt = vec![std::collections::HashMap::new(); period];
        cnt[0].insert(0, 1);

        let mut diff = 0;  // 元音数 - 辅音数
        let mut ans = 0;

        for (idx, &ch) in s.iter().enumerate() {
            // 更新 diff
            if ch == b'a' || ch == b'e' || ch == b'i' || ch == b'o' || ch == b'u' {
                diff += 1;
            } else {
                diff -= 1;
            }

            let pos = (idx + 1) % period;
            let entry = cnt[pos].entry(diff).or_insert(0);
            ans += *entry as i64;
            *entry += 1;
        }

        ans
    }
}
```
