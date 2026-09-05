---
title: "leetcode-前缀和16"
date: 2026-08-25T10:02:47+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 构建回文串检测

给你一个字符串 s，请你对 s 的子串进行检测。

每次检测，待检子串都可以表示为 queries[i] = [left, right, k]。我们可以 重新排列 子串 s[left], ..., s[right]，并从中选择 最多 k 项替换成任何小写英文字母。

如果在上述检测过程中，子串可以变成回文形式的字符串，那么检测结果为 true，否则结果为 false。

返回答案数组 answer[]，其中 answer[i] 是第 i 个待检子串 queries[i] 的检测结果。

注意：在替换时，子串中的每个字母都必须作为 独立的 项进行计数，也就是说，如果 s[left..right] = "aaa" 且 k = 2，我们只能替换其中的两个字母。（另外，任何检测都不会修改原始字符串 s，可以认为每次检测都是独立的）


```
impl Solution {
    pub fn can_make_pali_queries(s: String, queries: Vec<Vec<i32>>) -> Vec<bool> {
        let s = s.as_bytes();
        let n = s.len();

        // 前缀异或数组：prefix[i] 表示前 i 个字符的奇偶状态位掩码
        // 每个字符用 0-25 位表示，1 表示出现奇数次
        let mut prefix = vec![0u32; n + 1];
        for i in 0..n {
            prefix[i + 1] = prefix[i] ^ (1 << (s[i] - b'a'));
        }

        // 处理每个查询
        queries
            .into_iter()
            .map(|q| {
                let left = q[0] as usize;
                let right = q[1] as usize;
                let k = q[2];

                // 子串的奇偶状态
                let mask = prefix[right + 1] ^ prefix[left];
                // 出现奇数次的字符种类数
                let odd_count = mask.count_ones();
                // 需要替换的最小次数 = 奇数字符数 / 2
                // 因为每对奇数可以替换一个字符使回文成立
                odd_count / 2 <= k as u32
            })
            .collect()
    }
}
```
