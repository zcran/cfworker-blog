---
title: "leetcode-排序70"
date: 2026-10-05T00:35:04+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 重排子字符串以形成目标字符串

给你两个字符串 s 和 t（它们互为字母异位词），以及一个整数 k。

你的任务是判断是否可以将字符串 s 分割成 k 个等长的子字符串，然后重新排列这些子字符串，并以任意顺序连接它们，使得最终得到的新字符串与给定的字符串 t 相匹配。

如果可以做到，返回 true；否则，返回 false。

字母异位词 是指由另一个单词或短语的所有字母重新排列形成的单词或短语，使用所有原始字母恰好一次。

子字符串 是字符串中的一个连续 非空 字符序列。


```
impl Solution {
    pub fn is_possible_to_rearrange(s: String, t: String, k: i32) -> bool {
        let n = s.len();
        let len = n / k as usize; // 每个子串的长度

        // 将 s 和 t 分别按长度 len 切成 k 个子串
        let mut a: Vec<&str> = Vec::with_capacity(k as usize);
        let mut b: Vec<&str> = Vec::with_capacity(k as usize);
        for i in (0..n).step_by(len) {
            a.push(&s[i..i + len]);
            b.push(&t[i..i + len]);
        }

        // 排序后比较，等价于比较两个子串的多重集合是否相同
        a.sort_unstable();
        b.sort_unstable();

        a == b
    }
}
```
