---
title: "leetcode-位掩码41"
date: 2026-05-18T10:28:00+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## Hello LeetCode!


力扣嘉年华同样准备了纪念品展位，参观者只需要集齐 helloleetcode 的 13 张字母卡片即可获得力扣纪念章。

在展位上有一些由字母卡片拼成的单词，words[i][j] 表示第 i 个单词的第 j 个字母。

你可以从这些单词中取出一些卡片，但每次拿取卡片都需要消耗游戏代币，规则如下：

从一个单词中取一个字母所需要的代币数量，为该字母左边和右边字母数量之积

可以从一个单词中多次取字母，每个字母仅可被取一次

例如：从 example 中取出字母 a，需要消耗代币 2*4=8，字母取出后单词变为 exmple； 再从中取出字母 m，需要消耗代币 2*3=6，字母取出后单词变为 exple；

请返回取得 helloleetcode 这些字母需要消耗代币的 最少 数量。如果无法取得，返回 -1。

注意：

取出字母的顺序没有要求
取出的所有字母恰好可以拼成 helloleetcode


```
use std::collections::HashMap;

impl Solution {
    /// 从 words 中挑选子序列，拼成 "helloleetcode" (13 个字符)，
    /// 最小化删除字符的总代价（每个单词中删除某字符的代价 = 左边剩余字符数 * 右边剩余字符数）。
    /// 返回最小总代价，若不可能则返回 -1。
    pub fn leetcode(words: Vec<String>) -> i32 {
        const TARGET: &[u8] = b"helloleetcode"; // 13 个字符
        const M: usize = 13;
        const FULL: usize = (1 << M) - 1;
        const INF: i64 = i64::MAX / 4;

        // 将字符映射到 0..6 (共 7 种字母)
        fn char_id(ch: u8) -> Option<usize> {
            match ch {
                b'h' => Some(0),
                b'e' => Some(1),
                b'l' => Some(2),
                b'o' => Some(3),
                b't' => Some(4),
                b'c' => Some(5),
                b'd' => Some(6),
                _ => None,
            }
        }

        // 将 8 个 u8 打包成 32 位整数（每个 u8 占 4 位）
        fn pack(sig: &[u8; 8]) -> u32 {
            (0..8).fold(0, |acc, i| acc | ((sig[i] as u32) << (i * 4)))
        }

        // 预计算所有子集 mask 对应的签名 (7种字母计数 + 总个数)
        let mut sig_to_masks: HashMap<u32, Vec<usize>> = HashMap::new();
        for mask in 0usize..(1 << M) {  // 明确类型为 usize
            let mut sig = [0u8; 8];
            sig[7] = mask.count_ones() as u8;                 // 总字符数
            for i in 0..M {
                if (mask >> i) & 1 == 1 {
                    sig[char_id(TARGET[i]).unwrap()] += 1;   // 对应字母计数
                }
            }
            sig_to_masks.entry(pack(&sig)).or_default().push(mask);
        }

        // DP: dp[mask] = 最小代价，mask 表示已覆盖的目标字符集合
        let mut dp = vec![INF; 1 << M];
        dp[0] = 0;

        for word in words {
            let bytes = word.as_bytes();
            let len = bytes.len();
            if len == 0 { continue; }

            // 收集单词中属于目标字符的位置及其类型
            let mut pos = Vec::new();
            let mut kinds = Vec::new();
            for (i, &ch) in bytes.iter().enumerate() {
                if let Some(k) = char_id(ch) {
                    pos.push(i);
                    kinds.push(k);
                }
            }
            let k = pos.len();
            if k == 0 { continue; }

            let nsub = 1 << k;
            // 预先计算每个子集中 1 的个数
            let mut popcnt = vec![0u8; nsub];
            for m in 1..nsub {
                popcnt[m] = popcnt[m & (m - 1)] + 1;
            }

            // 计算每个子集对应的删除代价（即从单词中保留该子集字符的最小删除代价）
            let mut cost_sub = vec![INF; nsub];
            cost_sub[0] = 0;
            for mask in 1..nsub {
                let mut m = mask;
                while m != 0 {
                    let lsb = m & (!m + 1);
                    let t = lsb.trailing_zeros() as usize;
                    let prev = mask ^ lsb;

                    let left_removed = (prev & ((1 << t) - 1)).count_ones() as i64;
                    let total_removed = popcnt[prev] as i64;
                    let right_removed = total_removed - left_removed;

                    let p = pos[t] as i64;
                    let left = p - left_removed;
                    let right = (len as i64 - 1 - p) - right_removed;
                    let candidate = cost_sub[prev] + left * right;
                    if candidate < cost_sub[mask] {
                        cost_sub[mask] = candidate;
                    }
                    m ^= lsb;
                }
            }

            // 对于每个可能的子集，计算其对应的目标字符掩码（即该子集能覆盖的目标字符集合）
            let mut best = vec![INF; 1 << M];
            let mut touched = Vec::new(); // 记录被更新的目标掩码
            best[0] = 0;
            let mut sig = vec![[0u8; 8]; nsub];
            for sub in 1..nsub {
                let lsb = sub & (!sub + 1);
                let t = lsb.trailing_zeros() as usize;
                let prev = sub ^ lsb;
                let mut s = sig[prev];
                s[7] += 1;
                s[kinds[t]] += 1;
                sig[sub] = s;

                let c = cost_sub[sub];
                if c >= INF { continue; }
                if let Some(ms) = sig_to_masks.get(&pack(&s)) {
                    for &tm in ms {
                        if best[tm] == INF { touched.push(tm); }
                        if c < best[tm] { best[tm] = c; }
                    }
                }
            }

            // 收集有效的 (mask, cost) 对
            let opts: Vec<(usize, i64)> = touched
                .into_iter()
                .filter_map(|m| {
                    let c = best[m];
                    if c < INF { Some((m, c)) } else { None }
                })
                .collect();
            if opts.is_empty() { continue; }

            // 合并到全局 DP (类似背包，不可重复使用同一单词)
            let active: Vec<usize> = (0..(1 << M)).filter(|&m| dp[m] < INF).collect();
            let mut ndp = dp.clone();
            for &mask in &active {
                let base = dp[mask];
                for &(add, c) in &opts {
                    if (mask & add) != 0 { continue; }
                    let nm = mask | add;
                    let v = base + c;
                    if v < ndp[nm] { ndp[nm] = v; }
                }
            }
            dp = ndp;
        }

        let ans = dp[FULL];
        if ans >= INF { -1 } else { ans as i32 }
    }
}
```
