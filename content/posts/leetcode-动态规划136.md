---
title: "leetcode-动态规划136"
date: 2026-10-05T20:40:03+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 最小代价构造字符串

给你一个字符串 target、一个字符串数组 words 以及一个整数数组 costs，这两个数组长度相同。

设想一个空字符串 s。

你可以执行以下操作任意次数（包括 零 次）：

选择一个在范围  [0, words.length - 1] 的索引 i。
将 words[i] 追加到 s。
该操作的成本是 costs[i]。

返回使 s 等于 target 的 最小 成本。如果不可能，返回 -1。


```
use std::collections::VecDeque;

const INF: i64 = 1i64 << 60;

/// Aho-Corasick 自动机
/// - nxt: 转移表，nxt[u][c] 表示状态 u 读入字符 c 后转移到的状态
/// - fl:  失败指针
/// - dl:  沿失败链最近的「终止节点」（即某个单词的结尾状态）
/// - dep: 该状态对应的字符串深度（用于回退长度）
/// - cst: 该状态对应单词的最小成本
struct AhoCorasick {
    nxt: Vec<[u32; 26]>,
    fl: Vec<u32>,
    dl: Vec<u32>,
    dep: Vec<u32>,
    cst: Vec<i64>,
}

impl AhoCorasick {
    fn new() -> Self {
        Self {
            nxt: vec![[0u32; 26]],
            fl: vec![0],
            dl: vec![0],
            dep: vec![0],
            cst: vec![INF],
        }
    }

    fn insert(&mut self, word: &str, c: i64) {
        let mut u = 0usize;

        for b in word.bytes() {
            let idx = (b - b'a') as usize;
            let mut v = self.nxt[u][idx];

            if v == 0 {
                v = self.nxt.len() as u32;
                self.nxt[u][idx] = v;
                self.nxt.push([0u32; 26]);
                self.fl.push(0);
                self.dl.push(0);
                self.dep.push(self.dep[u] + 1);
                self.cst.push(INF);
            }

            u = v as usize;
        }

        if c < self.cst[u] {
            self.cst[u] = c;
        }
    }

    fn build(&mut self) {
        let mut q = VecDeque::new();

        // 初始化根节点的直接子节点
        for i in 0..26usize {
            let v = self.nxt[0][i];
            if v != 0 {
                q.push_back(v as usize);
            }
        }

        while let Some(u) = q.pop_front() {
            let f = self.fl[u] as usize;

            for j in 0..26usize {
                let v = self.nxt[u][j];

                if v != 0 {
                    let fv = self.nxt[f][j];
                    self.fl[v as usize] = fv;

                    // dl[v] 指向失败链上最近的终止节点
                    self.dl[v as usize] = if self.cst[fv as usize] < INF {
                        fv
                    } else {
                        self.dl[fv as usize]
                    };

                    q.push_back(v as usize);
                } else {
                    // 路径压缩：直接指向失败指针的对应转移
                    self.nxt[u][j] = self.nxt[f][j];
                }
            }
        }
    }
}

impl Solution {
    pub fn minimum_cost(target: String, words: Vec<String>, costs: Vec<i32>) -> i32 {
        let n = target.len();
        if n == 0 {
            return 0;
        }

        let mut ac = AhoCorasick::new();
        for (word, &cost) in words.iter().zip(&costs) {
            if !word.is_empty() && word.len() <= n {
                ac.insert(word, cost as i64);
            }
        }
        ac.build();

        // dp[i] = 拼出 target[..i] 的最小成本
        let mut dp = vec![INF; n + 1];
        dp[0] = 0;

        let mut st = 0usize;
        let AhoCorasick { nxt, cst, dl, dep, .. } = &ac;

        for (i, b) in target.bytes().enumerate() {
            let p = i + 1;
            st = nxt[st][(b - b'a') as usize] as usize;

            let mut best = dp[p];

            // 沿终止节点链回退，尝试所有以 p 结尾的单词
            let mut u = if cst[st] < INF { st } else { dl[st] as usize };
            while u != 0 {
                let len = dep[u] as usize;
                if p >= len && dp[p - len] < INF {
                    let cand = dp[p - len] + cst[u];
                    if cand < best {
                        best = cand;
                    }
                }
                u = dl[u] as usize;
            }

            dp[p] = best;
        }

        if dp[n] >= INF / 2 { -1 } else { dp[n] as i32 }
    }
}
```
