---
title: "leetcode-双指针107"
date: 2026-09-05T09:53:07+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 得到 K 个半回文串的最少修改次数

给你一个字符串 s 和一个整数 k，请将 s 划分为 k 个 非空子串 ，使得将每个子串变为 半回文串 所需的字符修改次数之和最小。

返回所需的 最少 字符修改次数。

半回文串 是一类特殊的字符串：它可以按照某种重复模式拆分后，使每一组都成为 回文串 。判断一个字符串是否为半回文串的方法如下：

1. 选择该字符串长度的一个正因数 d。其中，d 的取值范围是从 1 到严格小于字符串长度的所有正因数。对于长度为 1 的字符串，根据这一定义，它不存在合法的因数，因为唯一的因数就是其长度本身，而这是不允许的。
2. 对于给定的因数 d，将字符串按长度为 d 的重复模式分组。具体来说，第 1 组由位置 1、1 + d、1 + 2d、…… 上的字符组成；第 2 组由位置 2、2 + d、2 + 2d、…… 上的字符组成；以此类推。
3. 如果这些分组中的每一组都是回文串，则该字符串被视为半回文串。

以字符串 "abcabc" 为例：

· "abcabc" 的长度为 6。合法的因数有 1、2 和 3。
· 当 d = 1 时：整个字符串 "abcabc" 构成一组。它不是回文串。
· 当 d = 2 时：
    · 第 1 组（位置 1, 3, 5）："acb"
    · 第 2 组（位置 2, 4, 6）："bac"
    · 这两组都不是回文串。
· 当 d = 3 时：
    · 第 1 组（位置 1, 4）："aa"
    · 第 2 组（位置 2, 5）："bb"
    · 第 3 组（位置 3, 6）："cc"
    · 所有分组都是回文串。因此，"abcabc" 是一个半回文串。


```
impl Solution {
    pub fn minimum_changes(s: String, k: i32) -> i32 {
        const INF: i32 = 1_000_000;
        let s = s.as_bytes();
        let n = s.len();
        let k = k as usize;

        // 预计算每个长度的真因数（1 <= d < len）
        let mut divisors: Vec<Vec<usize>> = vec![Vec::new(); n + 1];
        for d in 1..=n {
            let mut len = 2 * d;
            while len <= n {
                divisors[len].push(d);
                len += d;
            }
        }

        // modify[i][j]：子串 s[i..=j] 变为半回文串的最小修改次数
        // 长度为 1 的子串不可行，保持 INF
        let mut modify = vec![vec![INF; n]; n];
        for i in 0..n {
            for j in (i + 1)..n {
                let len = j - i + 1;
                let mut best = INF;
                for &d in &divisors[len] {
                    let mut cnt = 0;
                    for g in 0..d {
                        let mut l = i + g;
                        let mut r = l + ((len - 1 - g) / d) * d;
                        while l < r {
                            if s[l] != s[r] {
                                cnt += 1;
                            }
                            l += d;
                            r -= d;
                        }
                    }
                    if cnt < best {
                        best = cnt;
                    }
                }
                modify[i][j] = best;
            }
        }

        // memo[j][t]：前 j+1 个字符划分为 t 段的最小修改次数，-1 表示未计算
        let mut memo = vec![vec![-1; k + 1]; n];

        fn dfs(j: usize, t: usize, n: usize, modify: &Vec<Vec<i32>>,
               memo: &mut Vec<Vec<i32>>, inf: i32) -> i32 {
            if t == 1 {
                return modify[0][j];
            }
            if memo[j][t] != -1 {
                return memo[j][t];
            }
            let mut res = inf;
            // 最后一段起点 start，长度至少为 2，且剩余前缀至少能分成 t-1 段（每段长度至少 2）
            let mut start = j.saturating_sub(1);
            while start >= 2 * (t - 1) {
                let m = modify[start][j];
                if m < inf {
                    let prev = dfs(start - 1, t - 1, n, modify, memo, inf);
                    if prev < inf {
                        let v = prev + m;
                        if v < res {
                            res = v;
                        }
                    }
                }
                start -= 1;
            }
            memo[j][t] = res;
            res
        }

        let ans = dfs(n - 1, k, n, &modify, &mut memo, INF);
        if ans >= INF { -1 } else { ans }
    }
}
```
