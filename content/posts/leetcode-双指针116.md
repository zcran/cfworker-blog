---
title: "leetcode-双指针116"
date: 2026-09-05T09:53:08+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 子字符串连接后的最长回文串 II

给你两个字符串 s 和 t。

你可以从 s 中选择一个子串（可以为空）以及从 t 中选择一个子串（可以为空），然后将它们 按顺序 连接，得到一个新的字符串。

返回可以由上述方法构造出的 最长 回文串的长度。

回文串 是指正着读和反着读都相同的字符串。

子字符串 是指字符串中的一个连续字符序列。


```
impl Solution {
    /// 从 s 和 t 中各选一个子串 x、y，按顺序拼接，返回能构造出的最长回文长度。
    ///
    /// 思路：设 P = x + y 是回文，则 P 的回文中心有两种位置：
    ///   1. 中心正好落在 s 与 t 的边界上（|x| == |y|）：
    ///      此时 x 与 y^R 逐字符相等。用「s 前缀对 t 后缀的反向 LCS」求出最长可镜像匹配长度 k，
    ///      贡献为 2k。
    ///   2. 中心落在 s 内部（|x| > |y|）：
    ///      枚举 s 的所有回文中心（共 2n-1 个），向两侧扩展得到以该点为中心的最长回文 s[l+1..=r-1]，
    ///      再在 s 的左侧前缀 s[0..=l] 与 t 的后缀之间做反向 LCS，作为两端可额外延展的字符数。
    ///
    /// 由于回文中心也可能落在 t 内部，故对 (s, t) 和 (rev_t, rev_s) 两种对称情形各算一次，取最大值。
    fn calc(s: &[u8], t: &[u8]) -> usize {
        let n = s.len();
        let m = t.len();

        // f[i+1][j]：s 的前 i+1 个字符与 t 的后 j' 个字符（反向遍历）做镜像匹配的最长长度
        // mx[i]：s 的前 i 个字符与 t 的反向匹配最大值
        let mut f = vec![vec![0usize; m + 1]; n + 1];
        let mut mx = vec![0usize; n + 1];

        for i in 0..n {
            for j in 0..m {
                if s[i] == t[j] {
                    // s[i] 与 t[j] 镜像配对，前驱状态用 f[i][j+1]
                    f[i + 1][j] = f[i][j + 1] + 1;
                }
            }
            mx[i + 1] = *f[i + 1].iter().max().unwrap();
        }

        // 情况 1：|x| == |y|，中心在 s / t 的边界上
        let mut ans = *mx.iter().max().unwrap() * 2;

        // 情况 2：|x| > |y|，中心在 s 内部，枚举 s 的所有回文中心
        let total = 2 * n;
        for i in 0..total.saturating_sub(1) {
            // 以 s[l+1..=r-1] 为中心扩展回文
            let mut l = (i / 2) as isize;
            let mut r = ((i + 1) / 2) as isize;
            while l >= 0 && (r as usize) < n && s[l as usize] == s[r as usize] {
                l -= 1;
                r += 1;
            }

            // s[l+1..=r-1] 为非空回文
            if l + 1 <= r - 1 {
                let left = (l + 1) as usize; // 可用作左侧扩展的 s 前缀长度
                let cand = (r - l - 1) as usize + mx[left] * 2;
                if cand > ans {
                    ans = cand;
                }
            }
        }

        ans
    }

    pub fn longest_palindrome(s: String, t: String) -> i32 {
        let sb = s.as_bytes();
        let tb = t.as_bytes();
        let rev_s: Vec<u8> = sb.iter().rev().copied().collect();
        let rev_t: Vec<u8> = tb.iter().rev().copied().collect();

        // 处理中心在 s 侧与中心在 t 侧的两种对称情形
        Self::calc(sb, tb).max(Self::calc(&rev_t, &rev_s)) as i32
    }
}
```
