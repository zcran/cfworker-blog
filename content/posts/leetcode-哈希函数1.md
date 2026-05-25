---
title: "leetcode-哈希函数1"
date: 2026-04-26T20:59:32+08:00
tags: ["leetcode", "哈希函数"]
draft: false
---


## 回文对

给定一个由唯一字符串构成的 0 索引 数组 words 。

回文对 是一对整数 (i, j) ，满足以下条件：

0 <= i, j < words.length，
i != j ，并且
words[i] + words[j]（两个字符串的连接）是一个回文串。
返回一个数组，它包含 words 中所有满足 回文对 条件的字符串。

你必须设计一个时间复杂度为 O(sum of words[i].length) 的算法。

```
// 针对 Palindrome Pairs 问题的滚动哈希解法（双模数，基数 32）
// 哈希函数： h = (h << 5) | x，然后加上一个与高 5 位相关的随机扰动（REM 表）
// 使用两个接近 2^64 的大素数作为模数，避免取模运算（改用比较和减法）
impl Solution {
    /// 寻找所有回文串对 (i, j) 使得 words[i] + words[j] 是回文
    pub fn palindrome_pairs(words: Vec<String>) -> Vec<Vec<i32>> {
        let n = words.len();
        // 计算每个单词的正向哈希和反向哈希（双通道）
        let (forward, backward): (Vec<[u64; 2]>, Vec<[u64; 2]>) = words
            .iter()
            .map(|s| {
                // 正向哈希：按原始顺序累积字节
                let f = s
                    .bytes()
                    .fold([0u64, 0u64], |mut h, c| {
                        Self::hash_update(&mut h, (c & 31) as u64);
                        h
                    });
                // 反向哈希：按逆序累积字节
                let b = s
                    .bytes()
                    .rev()
                    .fold([0u64, 0u64], |mut h, c| {
                        Self::hash_update(&mut h, (c & 31) as u64);
                        h
                    });
                (f, b)
            })
            .unzip(); // 解构为两个 Vec

        // 生成所有无序对 (i, j) 其中 i > j，并检查两种拼接顺序
        (0..n)
            .flat_map(|i| (0..i).map(move |j| (i, j))) // 仅上三角，避免重复
            .flat_map(|(i, j)| {
                let mut pairs = Vec::new();
                // 情况1：words[i] + words[j] 是回文
                if Self::hash_concat(forward[i], forward[j], words[j].len())
                    == Self::hash_concat(backward[j], backward[i], words[i].len())
                {
                    pairs.push(vec![i as i32, j as i32]);
                }
                // 情况2：words[j] + words[i] 是回文
                if Self::hash_concat(forward[j], forward[i], words[i].len())
                    == Self::hash_concat(backward[i], backward[j], words[j].len())
                {
                    pairs.push(vec![j as i32, i as i32]);
                }
                pairs
            })
            .collect()
    }

    // ----- 哈希核心函数（完全函数式，无副作用）-----

    /// 单步哈希更新（双通道就地修改，避免复制）
    /// 等价于原始的宏 `hash!` 扩展
    fn hash_update(h: &mut [u64; 2], x: u64) {
        Self::hash_update_channel(0, &mut h[0], x);
        Self::hash_update_channel(1, &mut h[1], x);
    }

    /// 单个通道的哈希更新（第 idx 个模数）
    /// 使用预计算的 REM 表实现快速模约减
    #[inline(always)]
    fn hash_update_channel(idx: usize, h: &mut u64, x: u64) {
        // 原算法：h = ((h << 5) | x) + REM[idx][h >> 59]   (模 P[idx])
        let (u, overflow) = h.wrapping_shl(5).overflowing_add(x);
        let add = REM[idx][(h.wrapping_shr(59)) as usize];
        let (u, carry) = u.overflowing_add(add);
        *h = if carry || overflow {
            u.wrapping_add(REM[idx][1])
        } else if u > P[idx] {
            u.wrapping_sub(P[idx])
        } else {
            u
        };
    }

    /// 连接两个单词的哈希值：hash(word1 + word2) = hash(word1) * BASE^len2 + hash(word2)
    /// 返回 128 位最终哈希（两个通道混合，用于最终比较）
    fn hash_concat(h1: [u64; 2], h2: [u64; 2], len2: usize) -> u128 {
        // 双通道分别做线性组合，模 P[idx]
        let mut h = [
            ((h1[0] as u128)
                .wrapping_mul(EXP[0][len2] as u128)
                .wrapping_add(h2[0] as u128)
                .wrapping_rem(P[0] as u128)) as u64,
            ((h1[1] as u128)
                .wrapping_mul(EXP[1][len2] as u128)
                .wrapping_add(h2[1] as u128)
                .wrapping_rem(P[1] as u128)) as u64,
        ];
        // 混合两个通道，增加安全性
        h[1] ^= h[0];
        // 安全地将两个 u64 组装为小端 u128，无需 unsafe
        (h[0] as u128) | ((h[1] as u128) << 64)
    }
}

// ----- 静态预计算表（与原始逻辑完全一致）-----
/// 双模数，均为接近 2^64 的大素数
const P: [u64; 2] = [!58, !82];          // 即 2^64-59 和 2^64-83
/// 最大单词长度（根据题目约束可调）
const MAX_STRLEN: usize = 300;
const M: usize = MAX_STRLEN + 1;          // 预计算表长度

/// 计算给定模数 p 的 REM 表（用于快速模约减）
/// REM[i] = 2^(5*i) mod p，但采用递推与条件减法实现
const fn rem(p: u64) -> [u64; 32] {
    assert!(p & !(u64::MAX >> 5) != 0);   // 保证 p 的高 5 位不全为0
    let mut ret = [0u64; 32];
    ret[1] = (u64::MAX - p).wrapping_add(1) % p; // (-p) mod p
    let mut i = 2;
    while i < 32 {
        let j = i >> 1;
        let (u, ov) = ret[j].overflowing_add(ret[j + (i & 1)]);
        ret[i] = if ov {
            u.wrapping_add(ret[1])
        } else if u > p {
            u.wrapping_sub(p)
        } else {
            u
        };
        i += 1;
    }
    ret
}

/// 预计算基数 32 的各次幂模 p（长度 M）
/// 使用 REM 表加速乘法
const fn exp<const N: usize>(idx: usize) -> [u64; N] {
    assert!(N > 0);
    let mut ret = [0u64; N];
    ret[0] = 1;
    let mut i = 1;
    while i < N {
        let (u, ov) = ret[i - 1]
            .wrapping_shl(5)
            .overflowing_add(REM[idx][(ret[i - 1].wrapping_shr(59)) as usize]);
        ret[i] = if ov {
            u.wrapping_add(REM[idx][1])
        } else if u > P[idx] {
            u.wrapping_sub(P[idx])
        } else {
            u
        };
        i += 1;
    }
    ret
}

/// 两个通道各自的 REM 表
const REM: [[u64; 32]; 2] = [rem(P[0]), rem(P[1])];
/// 两个通道各自的幂表
const EXP: [[u64; M]; 2] = [exp::<M>(0), exp::<M>(1)];
```
