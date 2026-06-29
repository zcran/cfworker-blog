---
title: "leetcode-并查集49"
date: 2026-06-25T11:22:23+08:00
tags: ["leetcode", "并查集"]
draft: false
---


## 找出对应 LCP 矩阵的字符串

对任一由 n 个小写英文字母组成的字符串 word ，我们可以定义一个 n x n 的矩阵，并满足：

lcp[i][j] 等于子字符串 word[i,...,n-1] 和 word[j,...,n-1] 之间的最长公共前缀的长度。
给你一个 n x n 的矩阵 lcp 。返回与 lcp 对应的、按字典序最小的字符串 word 。如果不存在这样的字符串，则返回空字符串。

对于长度相同的两个字符串 a 和 b ，如果在 a 和 b 不同的第一个位置，字符串 a 的字母在字母表中出现的顺序先于 b 中的对应字母，则认为字符串 a 按字典序比字符串 b 小。例如，"aabd" 在字典上小于 "aaca" ，因为二者不同的第一位置是第三个字母，而 'b' 先于 'c' 出现。




```
impl Solution {
    pub fn find_the_string(lcp: Vec<Vec<i32>>) -> String {
        let n = lcp.len();
        let mut word = vec![0u8; n]; // 0 表示未分配

        // 贪心构造：按字典序最小分配字符
        let mut next_char = b'a';
        for i in 0..n {
            if word[i] == 0 {
                // 需要新字符
                if next_char > b'z' {
                    return String::new(); // 超过26个字母，不可能
                }
                // 将 i 及所有与 i 有相同 LCP 的位置赋值为当前字符
                for j in i..n {
                    if lcp[i][j] > 0 {
                        // 如果 word[j] 已被分配且与当前字符不同，则冲突
                        if word[j] != 0 && word[j] != next_char {
                            return String::new();
                        }
                        word[j] = next_char;
                    }
                }
                // 确保 word[i] 本身被赋值（当 lcp[i][i] > 0 但可能为0？）
                // 实际上 lcp[i][i] 必须 > 0，但为了安全，显式赋值
                if word[i] == 0 {
                    word[i] = next_char;
                }
                next_char += 1;
            }
        }

        // 验证构造的字符串是否合法
        for i in (0..n).rev() {
            for j in (0..n).rev() {
                let expected = if word[i] == word[j] {
                    if i == n - 1 || j == n - 1 {
                        1
                    } else {
                        lcp[i + 1][j + 1] + 1
                    }
                } else {
                    0
                };
                if lcp[i][j] != expected {
                    return String::new();
                }
            }
        }

        String::from_utf8(word).unwrap_or_default()
    }
}
```
