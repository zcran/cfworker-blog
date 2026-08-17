---
title: "leetcode-计数99"
date: 2026-08-01T10:40:56+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 分割字符频率相等的最少子字符串

给你一个字符串 s ，你需要将它分割成一个或者更多的 平衡 子字符串。比方说，s == "ababcc" 那么 ("abab", "c", "c") ，("ab", "abc", "c") 和 ("ababcc") 都是合法分割，但是 ("a", "bab", "cc") ，("aba", "bc", "c") 和 ("ab", "abcc") 不是，不平衡的子字符串用粗体表示。

请你返回 s 最少 能分割成多少个平衡子字符串。

注意：一个 平衡 字符串指的是字符串中所有字符出现的次数都相同。


```
impl Solution {
    pub fn minimum_substrings_in_partition(s: String) -> i32 {
        let bytes = s.as_bytes();
        let n = bytes.len();
        let mut dp = vec![i32::MAX; n + 1];
        dp[0] = 0;

        for i in 1..=n {
            let mut freq = [0; 26];
            let mut max_cnt = 0;      // 当前段中的最大频率
            let mut distinct = 0;     // 当前段中不同字符的数量

            // 枚举最后一段的起始位置 j（从 i 往前扩展）
            for j in (1..=i).rev() {
                let idx = (bytes[j - 1] - b'a') as usize;

                // 首次出现该字符，不同字符数加一
                if freq[idx] == 0 {
                    distinct += 1;
                }
                freq[idx] += 1;
                max_cnt = max_cnt.max(freq[idx]);

                // 平衡条件：所有字符出现次数相同
                // 即 max_cnt * distinct == 段长度，且前面可分割
                let len = (i - j + 1) as i32;
                if max_cnt * distinct == len && dp[j - 1] != i32::MAX {
                    dp[i] = dp[i].min(dp[j - 1] + 1);
                }
            }
        }

        dp[n]
    }
}
```
