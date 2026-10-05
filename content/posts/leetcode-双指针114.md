---
title: "leetcode-双指针114"
date: 2026-09-05T09:53:08+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 字典序最小的合法序列

给你两个字符串 word1 和 word2 。

如果一个字符串 x 修改 至多 一个字符会变成 y ，那么我们称它与 y 几乎相等 。

如果一个下标序列 seq 满足以下条件，我们称它是 合法的 ：

下标序列是 升序 的。
将 word1 中这些下标对应的字符 按顺序 连接，得到一个与 word2 几乎相等 的字符串。

请你返回一个长度为 word2.length 的数组，表示一个 字典序最小 的 合法 下标序列。如果不存在这样的序列，请你返回一个 空 数组。

注意 ，答案数组必须是字典序最小的下标数组，而 不是 由这些下标连接形成的字符串。


```
impl Solution {
    /// 返回字典序最小的合法下标序列（长度 = word2.len()），使选出的字符与 word2 几乎相等
    /// （即至多修改一个字符后完全相同）；不存在则返回空数组。
    ///
    /// 思路：贪心 + 后缀匹配预处理。
    /// 1. 从后往前预处理 last[j]：word2[j..] 在 word1 中能匹配到的最靠后的起始下标，
    ///    用于判断「若当前跳过匹配」之后是否仍有足够空间完成剩余匹配。
    /// 2. 从左到右扫描 word1，优先选择最小的下标：
    ///    - 若 word1[i] == word2[j]，直接选，不消耗跳过机会；
    ///    - 否则若还没用过跳过机会，且选 i 后剩下的字符还能匹配 word2[j+1..]（即 i < last[j+1]），
    ///      则用掉跳过机会，选 i，跳过 word2[j]。
    ///
    /// 时间复杂度 O(n + m)，空间复杂度 O(m)。
    pub fn valid_sequence(word1: String, word2: String) -> Vec<i32> {
        let n = word1.len();
        let m = word2.len();
        let w1 = word1.as_bytes();
        let w2 = word2.as_bytes();

        // last[j]：word2[j..] 在 word1 中匹配时，word2[j] 可选的最靠后下标
        let mut last = vec![-1i32; m];
        let mut j = m as i32 - 1;
        for i in (0..n).rev() {
            if j >= 0 && w1[i] == w2[j as usize] {
                last[j as usize] = i as i32;
                j -= 1;
            }
        }

        let mut res = Vec::with_capacity(m);
        let mut skipped = false;
        let mut j = 0;

        for i in 0..n {
            if j == m {
                break;
            }

            if w1[i] == w2[j] {
                // 直接匹配，不消耗跳过机会
                res.push(i as i32);
                j += 1;
            } else if !skipped && (j == m - 1 || (i as i32) < last[j + 1]) {
                // 用掉唯一的跳过机会，跳过 word2[j]
                skipped = true;
                res.push(i as i32);
                j += 1;
            }
        }

        if j == m { res } else { vec![] }
    }
}
```
