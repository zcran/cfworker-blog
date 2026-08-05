---
title: "leetcode-枚举80"
date: 2026-07-09T10:05:03+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 子字符串连接后的最长回文串 I

给你两个字符串 s 和 t。

你可以从 s 中选择一个子串（可以为空）以及从 t 中选择一个子串（可以为空），然后将它们 按顺序 连接，得到一个新的字符串。

返回可以由上述方法构造出的 最长 回文串的长度。

回文串 是指正着读和反着读都相同的字符串。

子字符串 是指字符串中的一个连续字符序列。


```
impl Solution {
    pub fn longest_palindrome(s: String, t: String) -> i32 {
        let s_chars: Vec<char> = s.chars().collect();
        let t_chars: Vec<char> = t.chars().collect();
        let n = s_chars.len();
        let m = t_chars.len();

        // 预计算 s 中所有子串是否为回文
        let mut is_pal_s = vec![vec![false; n]; n];
        for i in 0..n {
            is_pal_s[i][i] = true;
            if i + 1 < n && s_chars[i] == s_chars[i + 1] {
                is_pal_s[i][i + 1] = true;
            }
        }
        for len in 3..=n {
            for i in 0..=n - len {
                let j = i + len - 1;
                if s_chars[i] == s_chars[j] && is_pal_s[i + 1][j - 1] {
                    is_pal_s[i][j] = true;
                }
            }
        }

        // 预计算 t 中所有子串是否为回文
        let mut is_pal_t = vec![vec![false; m]; m];
        for i in 0..m {
            is_pal_t[i][i] = true;
            if i + 1 < m && t_chars[i] == t_chars[i + 1] {
                is_pal_t[i][i + 1] = true;
            }
        }
        for len in 3..=m {
            for i in 0..=m - len {
                let j = i + len - 1;
                if t_chars[i] == t_chars[j] && is_pal_t[i + 1][j - 1] {
                    is_pal_t[i][j] = true;
                }
            }
        }

        let mut ans = 0;

        // 单个字符串的最长回文子串
        for i in 0..n {
            for j in i..n {
                if is_pal_s[i][j] {
                    ans = ans.max(j - i + 1);
                }
            }
        }
        for i in 0..m {
            for j in i..m {
                if is_pal_t[i][j] {
                    ans = ans.max(j - i + 1);
                }
            }
        }

        // 辅助：判断 s[i..i+lenA-1] 是否等于 t[p..p+lenB-1] 的逆序
        fn is_rev_equal(
            s: &[char], i: usize, len_a: usize,
            t: &[char], p: usize, len_b: usize,
        ) -> bool {
            if len_a != len_b {
                return false;
            }
            for k in 0..len_a {
                if s[i + k] != t[p + len_a - 1 - k] {
                    return false;
                }
            }
            true
        }

        // 枚举 s 的所有非空子串 A
        for i in 0..n {
            for j in i..n {
                let len_a = j - i + 1;
                // 枚举 t 的所有非空子串 B
                for p in 0..m {
                    for q in p..m {
                        let len_b = q - p + 1;

                        if len_a >= len_b {
                            // A 的前 len_b 个字符必须等于 B 的逆序
                            // 且 A 剩余部分 (i+len_b .. j) 是回文
                            if is_rev_equal(&s_chars, i, len_b, &t_chars, p, len_b)
                                && (len_a == len_b || is_pal_s[i + len_b][j])
                            {
                                ans = ans.max(len_a + len_b);
                            }
                        } else {
                            // B 的后 len_a 个字符必须等于 A 的逆序
                            // 且 B 剩余部分 (p .. q-len_a) 是回文
                            let start_b = q - len_a + 1;
                            if is_rev_equal(&s_chars, i, len_a, &t_chars, start_b, len_a)
                                && (len_b == len_a || is_pal_t[p][q - len_a])
                            {
                                ans = ans.max(len_a + len_b);
                            }
                        }
                    }
                }
            }
        }

        ans as i32
    }
}
```
