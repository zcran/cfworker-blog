---
title: "leetcode-位掩码23"
date: 2026-05-18T10:27:59+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 两个回文子序列长度的最大乘积

给你一个字符串 s ，请你找到 s 中两个 不相交回文子序列 ，使得它们长度的 乘积最大 。两个子序列在原字符串中如果没有任何相同下标的字符，则它们是 不相交 的。

请你返回两个回文子序列长度可以达到的 最大乘积 。

子序列 指的是从原字符串中删除若干个字符（可以一个也不删除）后，剩余字符不改变顺序而得到的结果。如果一个字符串从前往后读和从后往前读一模一样，那么这个字符串是一个 回文字符串 。

```
impl Solution {
    pub fn max_product(s: String) -> i32 {
        let s = s.as_bytes();
        let n = s.len();
        let total = 1 << n;

        // 用 fold 构建 best 数组
        let best = (1..total).fold(vec![0; total], |mut best, mask| {
            // 回文检测：函数式风格（使用 find 和 all）
            let indices: Vec<usize> = (0..n).filter(|&i| (mask >> i) & 1 == 1).collect();
            let is_pal = indices.iter().zip(indices.iter().rev())
                .take(indices.len() / 2)
                .all(|(&a, &b)| s[a] == s[b]);

            if is_pal {
                best[mask] = indices.len();
            } else {
                // 枚举所有真子集（不包括空集）取最大值
                let mut sub = (mask - 1) & mask;
                while sub != 0 {
                    best[mask] = best[mask].max(best[sub]);
                    sub = (sub - 1) & mask;
                }
            }
            best
        });

        let full = total - 1;
        (1..total / 2)
            .map(|mask| best[mask] * best[mask ^ full])
            .max()
            .unwrap_or(1) as i32
    }
}
```
