---
title: "leetcode-滚动哈希9"
date: 2026-04-17T20:00:31+08:00
tags: ["leetcode", "滚动哈希"]
draft: false
---


## 最长公共子路径

一个国家由 n 个编号为 0 到 n - 1 的城市组成。在这个国家里，每两个 城市之间都有一条道路连接。

总共有 m 个编号为 0 到 m - 1 的朋友想在这个国家旅游。他们每一个人的路径都会包含一些城市。每条路径都由一个整数数组表示，每个整数数组表示一个朋友按顺序访问过的城市序列。同一个城市在一条路径中可能 重复 出现，但同一个城市在一条路径中不会连续出现。

给你一个整数 n 和二维数组 paths ，其中 paths[i] 是一个整数数组，表示第 i 个朋友走过的路径，请你返回 每一个 朋友都走过的 最长公共子路径 的长度，如果不存在公共子路径，请你返回 0 。

一个 子路径 指的是一条路径中连续的城市序列。


```
use std::collections::HashSet;

/// 问题：给定 n 个城市（0..n-1）和 m 个朋友的旅行路径（每个路径是一个城市序列），
/// 要求找出所有朋友都走过的**最长公共子路径**（连续子序列）的长度。
/// 使用滚动哈希（Rabin-Karp）配合二分搜索实现。
/// 本解法采用**前缀哈希** + **双模数**的方法，以避免哈希碰撞。
/// 时间复杂度：O(total_len * log(min_len))，空间复杂度：O(total_len)。

/// 哈希参数（选择大质数和随机基数可降低碰撞概率）
const BASE: u64 = 1_000_003;          // 基数
const MOD1: u64 = 1_000_000_007;      // 第一个模数
const MOD2: u64 = 1_000_000_009;      // 第二个模数

impl Solution {
    pub fn longest_common_subpath(n: i32, paths: Vec<Vec<i32>>) -> i32 {
        // 如果没有路径或任何路径为空，直接返回 0
        if paths.is_empty() || paths.iter().any(|p| p.is_empty()) {
            return 0;
        }

        // 找到所有路径中最短的长度，作为二分搜索的上界
        let min_len = paths.iter().map(|p| p.len()).min().unwrap();

        // 所有路径中最长的长度，用于预计算幂数组
        let max_len = paths.iter().map(|p| p.len()).max().unwrap();

        // ---------- 预计算 BASE 的幂（模两个模数）----------
        let mut pow1 = vec![1u64; max_len + 1];
        let mut pow2 = vec![1u64; max_len + 1];
        for i in 1..=max_len {
            pow1[i] = (pow1[i - 1] * BASE) % MOD1;
            pow2[i] = (pow2[i - 1] * BASE) % MOD2;
        }

        // ---------- 为每条路径计算前缀哈希（双哈希）----------
        // 每条路径对应一个元组 (pre1, pre2)，其中 pre1[k] 表示前 k 个元素的哈希（模 MOD1）
        let prefix_hashes: Vec<(Vec<u64>, Vec<u64>)> = paths
            .iter()
            .map(|path| {
                let mut pre1 = vec![0u64; path.len() + 1];
                let mut pre2 = vec![0u64; path.len() + 1];
                for (i, &city) in path.iter().enumerate() {
                    // 将城市编号转换为 u64（题目保证非负）
                    let val = city as u64;
                    pre1[i + 1] = (pre1[i] * BASE + val) % MOD1;
                    pre2[i + 1] = (pre2[i] * BASE + val) % MOD2;
                }
                (pre1, pre2)
            })
            .collect();

        // 辅助函数：获取子串 [l, r) 的哈希（双哈希）
        // 参数 l 为起始下标（包含），r 为结束下标（不包含），长度 len = r - l
        let get_hash = |pre1: &[u64], pre2: &[u64], l: usize, r: usize| -> (u64, u64) {
            let len = r - l;
            let h1 = (pre1[r] + MOD1 - (pre1[l] * pow1[len]) % MOD1) % MOD1;
            let h2 = (pre2[r] + MOD2 - (pre2[l] * pow2[len]) % MOD2) % MOD2;
            (h1, h2)
        };

        // ---------- 检查函数：是否存在长度为 L 的公共子路径 ----------
        let check = |L: usize| -> bool {
            if L == 0 {
                return true;
            }
            // 遍历每条路径，收集其所有长度为 L 的子串哈希（双哈希）
            // 使用函数式风格：将第一个路径的哈希集合作为初始集合，然后依次与后续路径的集合求交集
            let mut common_set: Option<HashSet<(u64, u64)>> = None;

            for (pre1, pre2) in &prefix_hashes {
                let len = pre1.len() - 1; // 路径实际长度
                if len < L {
                    // 当前路径长度不足 L，则不可能有公共子路径
                    return false;
                }
                // 生成当前路径所有长度为 L 的子串哈希集合
                let set: HashSet<(u64, u64)> = (0..=len - L)
                    .map(|i| get_hash(pre1, pre2, i, i + L))
                    .collect();

                match common_set {
                    None => common_set = Some(set), // 第一条路径，直接使用
                    Some(ref mut acc) => {
                        // 求交集：只保留 acc 中也出现在当前 set 中的哈希
                        // 这里用函数式的 retain 改写为迭代器过滤
                        let new_acc: HashSet<_> = acc
                            .iter()
                            .filter(|h| set.contains(h))
                            .copied()
                            .collect();
                        *acc = new_acc;
                        if acc.is_empty() {
                            return false; // 交集已空，提前结束
                        }
                    }
                }
            }
            common_set.map_or(false, |s| !s.is_empty())
        };

        // ---------- 二分搜索最长长度 ----------
        let mut left = 0;
        let mut right = min_len;
        let mut ans = 0;
        while left <= right {
            let mid = left + (right - left) / 2;
            if check(mid) {
                ans = mid;
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        ans as i32
    }
}
```
