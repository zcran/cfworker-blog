---
title: "leetcode-双指针132"
date: 2026-09-05T09:53:09+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 一次替换后的子序列

给你两个由小写英文字母组成的字符串 s 和 t。

你最多可以选择 s 中的一个下标，并将该下标处的字符 替换 为任意小写英文字母。

如果可以使 s 成为 t 的一个 子序列，则返回 true；否则返回 false。

子序列 是指通过删除另一个字符串中的某些字符或不删除任何字符，并且不改变剩余字符相对顺序后得到的字符串。


```
impl Solution {
    pub fn can_make_subsequence(s: String, t: String) -> bool {
        let s = s.as_bytes();
        let t = t.as_bytes();
        let n = s.len();
        let m = t.len();

        // suf[i]：s[suf[i]..] 是 t[i..] 的子序列（贪心匹配的最左起点）
        let mut suf = vec![0usize; m + 1];
        suf[m] = n;
        let mut j = n;
        for i in (0..m).rev() {
            if j > 0 && s[j - 1] == t[i] {
                j -= 1;
                if j == 0 {
                    // s 本身已是 t 的子序列
                    return true;
                }
            }
            suf[i] = j;
        }

        // pre：s[..=pre] 是 t[..i] 的子序列
        let mut pre: isize = -1;
        for i in 0..m {
            // 替换 s[pre+1] 后，s[pre+2..] 需能匹配 t[i+1..]
            if (pre + 2) as usize == suf[i + 1] {
                return true;
            }
            if s[(pre + 1) as usize] == t[i] {
                pre += 1;
            }
        }

        false
    }
}
```
