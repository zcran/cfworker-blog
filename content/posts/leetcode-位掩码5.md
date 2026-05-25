---
title: "leetcode-位掩码5"
date: 2026-05-18T10:27:58+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 最短超级串

给定一个字符串数组 words，找到以 words 中每个字符串作为子字符串的最短字符串。如果有多个有效最短字符串满足题目条件，返回其中 任意一个 即可。

我们可以假设 words 中没有字符串是 words 中另一个字符串的子字符串。

```
impl Solution {
    pub fn shortest_superstring(words: Vec<String>) -> String {
        let n = words.len();
        if n == 0 { return String::new(); }
        if n == 1 { return words[0].clone(); }

        // 重叠矩阵（同命令式）
        let mut overlap = vec![vec![0; n]; n];
        for i in 0..n {
            for j in 0..n {
                if i == j { continue; }
                let wi = &words[i];
                let wj = &words[j];
                let max_k = wi.len().min(wj.len());
                overlap[i][j] = (0..=max_k)
                    .rev()
                    .find(|&k| wi.ends_with(&wj[0..k]))
                    .unwrap_or(0);
            }
        }

        let full_mask = (1 << n) - 1;
        let mut dp = vec![vec![0; n]; 1 << n];
        let mut parent = vec![vec![n; n]; 1 << n];

        // 函数式 DP：使用 for 循环是必要的（迭代器模拟 fold 嵌套复杂），但内部使用迭代器
        for mask in 1..=full_mask {
            for i in 0..n {
                if (mask >> i) & 1 == 0 { continue; }
                let prev_mask = mask ^ (1 << i);
                if prev_mask == 0 { continue; }
                // 用迭代器找出最佳前驱
                let best = (0..n)
                    .filter(|&j| (prev_mask >> j) & 1 == 1)
                    .map(|j| (j, dp[prev_mask][j] + overlap[j][i]))
                    .max_by_key(|&(_, val)| val);
                if let Some((best_j, best_val)) = best {
                    dp[mask][i] = best_val;
                    parent[mask][i] = best_j;
                }
            }
        }

        // 找出最优结尾（迭代器风格）
        let best_last = (0..n)
            .max_by_key(|&i| dp[full_mask][i])
            .unwrap();

        // 回溯（只能用循环）
        let mut path = Vec::with_capacity(n);
        let mut mask = full_mask;
        let mut cur = best_last;
        while cur != n {
            path.push(cur);
            let next = parent[mask][cur];
            mask ^= 1 << cur;
            cur = next;
        }
        path.reverse();

        // 构建结果（迭代器风格 fold）
        let result = path.iter().fold(String::new(), |mut acc, &idx| {
            if acc.is_empty() {
                words[idx].clone()
            } else {
                let prev = *path.get(path.iter().position(|&x| x == idx).unwrap() - 1).unwrap();
                let ov = overlap[prev][idx];
                acc.push_str(&words[idx][ov..]);
                acc
            }
        });
        result
    }
}
```
