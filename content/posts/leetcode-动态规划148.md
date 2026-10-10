---
title: "leetcode-动态规划148"
date: 2026-10-05T20:40:04+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 至多 K 次操作后的最长回文子序列

给你一个字符串 s 和一个整数 k。

在一次操作中，你可以将任意位置的字符替换为字母表中相邻的字符（字母表是循环的，因此 'z' 的下一个字母是 'a'）。例如，将 'a' 替换为下一个字母结果是 'b'，将 'a' 替换为上一个字母结果是 'z'；同样，将 'z' 替换为下一个字母结果是 'a'，替换为上一个字母结果是 'y'。

返回在进行 最多 k 次操作后，s 的 最长回文子序列 的长度。

子序列 是一个 非空 字符串，可以通过删除原字符串中的某些字符（或不删除任何字符）并保持剩余字符的相对顺序得到。

回文 是正着读和反着读都相同的字符串。


```
impl Solution {
    pub fn longest_palindromic_subsequence(s: String, k: i32) -> i32 {
        let k = k as usize;
        let s = s.as_bytes();
        let n = s.len();

        // 循环字母表下两个字符互相转换的最小操作数
        let dist = |a: u8, b: u8| -> usize {
            let d = (a as i32 - b as i32).unsigned_abs() as usize;
            d.min(26 - d)
        };

        // 若把整个字符串变成回文所需操作不超过 k，则答案就是 n
        let total: usize = (0..n / 2).map(|i| dist(s[i], s[n - 1 - i])).sum();
        if total <= k {
            return n as i32;
        }

        // f[k][i][j]：在子串 s[i..=j] 上最多花 k 次操作能得到的最长回文子序列长度
        // 使用二维滚动：按区间长度从小到大递推
        let mut f = vec![vec![vec![0i32; n]; n]; k + 1];

        for kk in 0..=k {
            for i in (0..n).rev() {
                f[kk][i][i] = 1;
                for j in i + 1..n {
                    let mut res = f[kk][i + 1][j].max(f[kk][i][j - 1]);

                    let op = dist(s[i], s[j]);
                    if op <= kk {
                        res = res.max(f[kk - op][i + 1][j - 1] + 2);
                    }

                    f[kk][i][j] = res;
                }
            }
        }

        f[k][0][n - 1]
    }
}
```
