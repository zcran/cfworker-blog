---
title: "leetcode-贪心152"
date: 2026-10-03T12:55:44+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 选择 K 个互不重叠的特殊子字符串

给你一个长度为 n 的字符串 s 和一个整数 k，判断是否可以选择 k 个互不重叠的 特殊子字符串 。

特殊子字符串 是满足以下条件的子字符串：

子字符串中的任何字符都不应该出现在字符串其余部分中。

子字符串不能是整个字符串 s。

注意：所有 k 个子字符串必须是互不重叠的，即它们不能有任何重叠部分。

如果可以选择 k 个这样的互不重叠的特殊子字符串，则返回 true；否则返回 false。

子字符串 是字符串中的连续、非空字符序列。


```
impl Solution {
    pub fn max_substring_length(s: String, k: i32) -> bool {
        if k == 0 {
            return true;
        }
        let n = s.len();
        let s = s.as_bytes();

        // 后缀计数：sufcnt[i][c] = s[i..] 中字符 c 的出现次数
        let mut sufcnt = vec![[0i32; 26]; n + 1];
        for i in (0..n).rev() {
            sufcnt[i] = sufcnt[i + 1];
            sufcnt[i][(s[i] - b'a') as usize] += 1;
        }

        // 并查集：把必须出现在同一特殊子串中的位置合并在一起。
        // 若某字符 c 在 i 之前出现过，则从上一个出现位置到 i 的整段
        // 都被"夹"在 c 的两次出现之间，必须合并成一个整体。
        struct UnionFind {
            leader: Vec<usize>,
        }
        impl UnionFind {
            fn new(n: usize) -> Self {
                Self { leader: (0..n).collect() }
            }
            fn find(&mut self, mut x: usize) -> usize {
                while self.leader[x] != x {
                    self.leader[x] = self.leader[self.leader[x]];
                    x = self.leader[x];
                }
                x
            }
            fn union(&mut self, a: usize, b: usize) {
                let (ra, rb) = (self.find(a), self.find(b));
                if ra != rb {
                    self.leader[ra.max(rb)] = ra.min(rb);
                }
            }
        }
        let mut uf = UnionFind::new(n);
        let mut first = [usize::MAX; 26];

        // dp[i][r]：考虑前 i 个字符，能否选出 r 个互不重叠的特殊子串
        let mut dp = vec![vec![false; k as usize + 1]; n + 1];
        dp[0][0] = true;

        for i in 0..n {
            // 不选以 i 结尾的子串：继承 dp[i]
            dp[i + 1] = dp[i].clone();

            let c = (s[i] - b'a') as usize;
            if first[c] != usize::MAX {
                // 将 [first[c], i] 区间内所有位置合并
                let mut j = i;
                while uf.find(j) != uf.find(first[c]) {
                    uf.union(j - 1, j);
                    j = uf.find(j - 1);
                }
            }

            // j 是以 i 结尾的、可能成为特殊子串的最左起点
            let j = uf.find(i);

            // 检查 [j, i] 是否为合法特殊子串：
            // 其中每个字符都不能出现在 s[i+1..] 中
            let mut ok = true;
            for c in 0..26 {
                if sufcnt[j][c] - sufcnt[i + 1][c] > 0 && sufcnt[i + 1][c] > 0 {
                    ok = false;
                    break;
                }
            }

            if ok {
                // 不能取整个字符串
                let is_whole = j == 0 && i == n - 1;
                for r in 1..=k as usize {
                    dp[i + 1][r] |= dp[j][r - 1];
                }
                if dp[i + 1][k as usize] && !is_whole {
                    return true;
                }
            }

            if first[c] == usize::MAX {
                first[c] = i;
            }
        }

        false
    }
}
```
