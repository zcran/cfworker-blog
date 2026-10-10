---
title: "leetcode-动态规划85"
date: 2026-10-05T20:39:59+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 通过给定词典构造目标字符串的方案数

给你一个字符串列表 words 和一个目标字符串 target 。words 中所有字符串都 长度相同  。

你的目标是使用给定的 words 字符串列表按照下述规则构造 target ：

从左到右依次构造 target 的每一个字符。
为了得到 target 第 i 个字符（下标从 0 开始），当 target[i] = words[j][k] 时，你可以使用 words 列表中第 j 个字符串的第 k 个字符。
一旦你使用了 words 中第 j 个字符串的第 k 个字符，你不能再使用 words 字符串列表中任意单词的第 x 个字符（x <= k）。也就是说，所有单词下标小于等于 k 的字符都不能再被使用。
请你重复此过程直到得到目标字符串 target 。

请注意， 在构造目标字符串的过程中，你可以按照上述规定使用 words 列表中 同一个字符串 的 多个字符 。

请你返回使用 words 构造 target 的方案数。由于答案可能会很大，请对 10^9 + 7 取余 后返回。

（译者注：此题目求的是有多少个不同的 k 序列，详情请见示例。）


```
impl Solution {
    pub fn num_ways(words: Vec<String>, target: String) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let target = target.as_bytes();
        let m = target.len();
        let n = words[0].len();

        // cnt[j][c]: 第 j 列中字符 c 出现的次数
        let mut cnt = vec![[0i64; 26]; n];
        for w in &words {
            for (j, &b) in w.as_bytes().iter().enumerate() {
                cnt[j][(b - b'a') as usize] += 1;
            }
        }

        // dp[j]: 从 words 第 j 列开始，为当前处理的后缀（target[i..]）选择字符的方案数
        // 初始处理空后缀：任意起点都只有 1 种方案（不做任何选择），故全为 1
        let mut dp = vec![1i64; n + 1];

        // 从 target 末尾向前逐个字符处理
        for i in (0..m).rev() {
            let c = (target[i] - b'a') as usize;
            let mut ndp = vec![0i64; n + 1];
            let mut suffix = 0i64;

            // 从右往左维护后缀和：
            // ndp[j] = Σ_{k >= j} cnt[k][c] * dp[k+1]
            for j in (0..n).rev() {
                suffix = (suffix + cnt[j][c] * dp[j + 1]) % MOD;
                ndp[j] = suffix;
            }
            dp = ndp;
        }

        dp[0] as i32
    }
}
```
