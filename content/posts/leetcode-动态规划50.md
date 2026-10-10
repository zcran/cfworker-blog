---
title: "leetcode-动态规划50"
date: 2026-10-05T20:39:57+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 统计子串中的唯一字符

我们定义了一个函数 countUniqueChars(s) 来统计字符串 s 中的唯一字符，并返回唯一字符的个数。

例如：s = "LEETCODE" ，则其中 "L", "T","C","O","D" 都是唯一字符，因为它们只出现一次，所以 countUniqueChars(s) = 5 。

本题将会给你一个字符串 s ，我们需要返回 countUniqueChars(t) 的总和，其中 t 是 s 的子字符串。输入用例保证返回值为 32 位整数。

注意，某些子字符串可能是重复的，但你统计时也必须算上这些重复的子字符串（也就是说，你必须统计 s 的所有子字符串中的唯一字符）。




```
impl Solution {
    /// 828. 统计子串中的唯一字符
    ///
    /// 换个角度：不枚举子串，而是统计每个字符对答案的贡献。
    /// 对字符 c 在位置 i 的出现，它能作为"唯一字符"被计入的子串，
    /// 必须包含 i，且不包含 c 在 i 左边最近的出现位置 L 和右边最近的出现位置 R。
    /// 这样的子串左端点有 (i - L) 种，右端点有 (R - i) 种，贡献为 (i - L) * (R - i)。
    ///
    /// 对每个字符维护其所有出现位置（两端补哨兵 -1 和 len），
    /// 相邻三元组 (L, i, R) 即可算出该次出现的贡献，累加即答案。
    pub fn unique_letter_string(s: String) -> i32 {
        let n = s.len() as i32;
        // pos[c] 保存字符 c 在 s 中所有下标，初始放入哨兵 -1
        let mut pos: Vec<Vec<i32>> = vec![vec![-1]; 26];
        for (i, b) in s.bytes().enumerate() {
            pos[(b - b'A') as usize].push(i as i32);
        }

        let mut ans = 0;
        for v in pos.iter_mut() {
            v.push(n); // 右哨兵
            // 相邻三元组 (v[i-1], v[i], v[i+1]) 即某次出现的 (L, i, R)
            for i in 1..v.len() - 1 {
                ans += (v[i] - v[i - 1]) * (v[i + 1] - v[i]);
            }
        }
        ans
    }
}
```
