---
title: "leetcode-哈希表40"
date: 2026-10-09T20:07:57+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## K 次操作转变字符串

给你两个字符串 s 和 t ，你的目标是在 k 次操作以内把字符串 s 转变成 t 。

在第 i 次操作时（1 <= i <= k），你可以选择进行如下操作：

选择字符串 s 中满足 1 <= j <= s.length 且之前未被选过的任意下标 j （下标从 1 开始），并将此位置的字符切换 i 次。

不进行任何操作。

切换 1 个字符的意思是用字母表中该字母的下一个字母替换它（字母表环状接起来，所以 'z' 切换后会变成 'a'）。第 i 次操作意味着该字符应切换 i 次

请记住任意一个下标 j 最多只能被操作 1 次。

如果在不超过 k 次操作内可以把字符串 s 转变成 t ，那么请你返回 true ，否则请你返回 false 。


```
impl Solution {
    pub fn can_convert_string(s: String, t: String, k: i32) -> bool {
        // 对每个需要修改的位置，计算它需要的切换次数 d ∈ [1, 25]。
        // 第 i 次操作可将某字符切换 i 次，因此需要 d 次的字符必须在第 d 次操作完成，
        // 且每轮只能处理一个同 d 的字符（下标不重复）。
        // 统计每种 d 的数量 cnt[d]，检查能否在前 k 次操作内全部安排：
        // 第 d 次操作每 26 轮出现一次（d, d+26, d+52, ...），共有 floor((k-d)/26)+1 次机会，
        // 需满足 cnt[d] <= 该值。d=0（已相同）无需操作。
        let (s, t) = (s.as_bytes(), t.as_bytes());
        if s.len() != t.len() {
            return false;
        }
        let k = k as usize;

        let mut cnt = [0usize; 26];
        for i in 0..s.len() {
            let d = (t[i] + 26 - s[i]) % 26;
            if d > 0 {
                cnt[d as usize] += 1;
            }
        }

        for d in 1..26 {
            // 第 d 次操作的机会数
            if cnt[d] > if k >= d { (k - d) / 26 + 1 } else { 0 } {
                return false;
            }
        }
        true
    }
}
```
