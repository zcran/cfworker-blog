---
title: "leetcode-哈希表85"
date: 2026-10-09T20:08:01+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 统计残差前缀

给你一个仅由小写英文字母组成的字符串 s。

如果字符串 s 的某个 前缀 中 不同字符的数量 等于 len(prefix) % 3，则该前缀被称为残差前缀（residue）。

返回字符串 s 中 残差前缀 的数量。

字符串的 前缀 是一个 非空子字符串，从字符串的开头起始并延伸到任意位置。


```
impl Solution {
    pub fn residue_prefixes(s: String) -> i32 {
        // 用 26 位掩码记录前缀中出现过的不同字符
        let mut mask = 0u32;
        let mut ans = 0;

        for (i, b) in s.bytes().enumerate() {
            mask |= 1 << (b - b'a');
            let distinct = mask.count_ones();

            // 不同字符数达到 3 后不可能再满足条件（因 len%3 最多为 2），可提前结束
            if distinct >= 3 {
                break;
            }
            if distinct == (i as u32 + 1) % 3 {
                ans += 1;
            }
        }

        ans
    }
}
```
