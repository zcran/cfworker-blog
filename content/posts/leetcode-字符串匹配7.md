---
title: "leetcode-字符串匹配7"
date: 2026-04-20T20:32:10+08:00
tags: ["leetcode", "字符串匹配"]
draft: false
---


## 找到所有好字符串

给你两个长度为 n 的字符串 s1 和 s2 ，以及一个字符串 evil 。请你返回 好字符串 的数目。

好字符串 的定义为：它的长度为 n ，字典序大于等于 s1 ，字典序小于等于 s2 ，且不包含 evil 为子字符串。

由于答案可能很大，请你返回答案对 10^9 + 7 取余的结果。

```
impl Solution {
    /// 计算字典序在 `s1` 和 `s2` 之间（含边界），且不包含子串 `evil` 的字符串数量。
    /// 结果对 `1_000_000_007` 取模。
    ///
    /// # 参数
    /// - `n`: 字符串长度，但实际由 `s1` 或 `s2` 长度决定（本题参数冗余，忽略）。
    /// - `s1`: 下界字符串
    /// - `s2`: 上界字符串
    /// - `evil`: 禁止出现的模式串
    pub fn find_good_strings(n: i32, s1: String, s2: String, evil: String) -> i32 {
        let solver = DpSolver::new(s1, s2, evil);
        solver.solve()
    }
}

/// 数位 DP 求解器
struct DpSolver {
    s1: Vec<u8>,                     // 下界字符的字节表示
    s2: Vec<u8>,                     // 上界字符的字节表示
    evil: Vec<u8>,                   // 禁止模式串的字节表示
    fail: Vec<usize>,                // KMP 失配数组
    trans: Vec<Vec<Option<usize>>>,  // 状态转移表（自动机）
    memo: Vec<Vec<Vec<Option<i32>>>>, // 记忆化数组：[pos][stats][bound]
    mod_val: i32,
}

impl DpSolver {
    const MOD: i32 = 1_000_000_007;

    /// 初始化 DP 求解器
    fn new(s1: String, s2: String, evil: String) -> Self {
        let s1 = s1.into_bytes();
        let s2 = s2.into_bytes();
        let evil = evil.into_bytes();
        let m = evil.len();
        let n = s1.len();

        // 构建 KMP fail 数组
        let mut fail = vec![0; m];
        for i in 1..m {
            let mut j = fail[i - 1];
            while j > 0 && evil[j] != evil[i] {
                j = fail[j - 1];
            }
            if evil[j] == evil[i] {
                j += 1;
            }
            fail[i] = j;
        }

        // 状态转移表 trans[stats][char_idx]（懒加载，用 Option 表示未计算）
        let trans = vec![vec![None; 26]; m];

        // 记忆化数组：pos 0..=n，stats 0..=m，bound 0..=3
        // bound 用两位二进制表示：bit0 表示是否紧贴 s1 下界，bit1 表示是否紧贴 s2 上界
        let memo = vec![vec![vec![None; 4]; m + 1]; n + 1];

        Self {
            s1,
            s2,
            evil,
            fail,
            trans,
            memo,
            mod_val: Self::MOD,
        }
    }

    /// 获取状态转移结果：当前 KMP 状态 stats 下读入字符 ch 后的新状态
    fn get_trans(&mut self, stats: usize, ch: u8) -> usize {
        let idx = (ch - b'a') as usize;
        if let Some(res) = self.trans[stats][idx] {
            return res;
        }

        let mut j = stats;
        while j > 0 && self.evil[j] != ch {
            j = self.fail[j - 1];
        }
        let next_stats = if self.evil[j] == ch { j + 1 } else { 0 };
        self.trans[stats][idx] = Some(next_stats);
        next_stats
    }

    /// 记忆化搜索（递归）
    fn dfs(&mut self, pos: usize, stats: usize, bound: u8) -> i32 {
        // 命中 evil 则非法
        if stats == self.evil.len() {
            return 0;
        }
        // 已构造完整字符串，合法
        if pos == self.s1.len() {
            return 1;
        }
        // 记忆化查询
        if let Some(val) = self.memo[pos][stats][bound as usize] {
            return val;
        }

        // 确定当前位可用的字符范围
        let lo = if bound & 1 != 0 { self.s1[pos] } else { b'a' };
        let hi = if bound & 2 != 0 { self.s2[pos] } else { b'z' };

        let mut total = 0;
        for ch in lo..=hi {
            let next_stats = self.get_trans(stats, ch);

            // 计算新的 bound 状态
            let mut next_bound = 0;
            if bound & 1 != 0 && ch == self.s1[pos] {
                next_bound |= 1;
            }
            if bound & 2 != 0 && ch == self.s2[pos] {
                next_bound |= 2;
            }

            total += self.dfs(pos + 1, next_stats, next_bound);
            total %= self.mod_val;
        }

        self.memo[pos][stats][bound as usize] = Some(total);
        total
    }

    /// 启动求解
    fn solve(mut self) -> i32 {
        self.dfs(0, 0, 3) // bound = 3 表示起始时同时紧贴上下界
    }
}
```
