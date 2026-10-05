---
title: "leetcode-广度优先搜索18"
date: 2026-09-13T09:32:07+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 相似度为 K 的字符串

对于某些非负整数 k ，如果交换 s1 中两个字母的位置恰好 k 次，能够使结果字符串等于 s2 ，则认为字符串 s1 和 s2 的 相似度为 k 。

给你两个字母异位词 s1 和 s2 ，返回 s1 和 s2 的相似度 k 的最小值。


```
use std::collections::HashMap;

impl Solution {
    pub fn k_similarity(s1: String, s2: String) -> i32 {
        let mut a = s1.into_bytes();
        let b = s2.into_bytes();
        let mut memo: HashMap<Vec<u8>, i32> = HashMap::new();
        Self::dfs(&mut a, &b, &mut memo)
    }

    fn dfs(a: &mut [u8], b: &[u8], memo: &mut HashMap<Vec<u8>, i32>) -> i32 {
        let n = a.len();

        // 跳过已匹配的前缀，找到第一个不匹配位置
        let mut i = 0;
        while i < n && a[i] == b[i] {
            i += 1;
        }
        if i == n {
            return 0;
        }

        // 查记忆表
        let key = a.to_vec();
        if let Some(&v) = memo.get(&key) {
            return v;
        }

        let mut best = i32::MAX;

        // 优先尝试 2-cycle：一次交换修正两个位置，通常能极大剪枝
        for j in i + 1..n {
            if a[j] == b[i] && b[j] == a[i] {
                a.swap(i, j);
                let r = Self::dfs(a, b, memo);
                a.swap(i, j);
                if r + 1 < best {
                    best = r + 1;
                }
                memo.insert(key, best);
                return best; // 启发式：2-cycle 分支视作最优，直接返回
            }
        }

        // 普通分支：把某个 a[j] == b[i] 的字符换到 i
        for j in i + 1..n {
            if a[j] == b[i] && a[j] != b[j] {
                a.swap(i, j);
                let r = Self::dfs(a, b, memo);
                a.swap(i, j);
                if r + 1 < best {
                    best = r + 1;
                }
            }
        }

        memo.insert(key, best);
        best
    }
}
```
