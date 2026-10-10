---
title: "leetcode-动态规划124"
date: 2026-10-05T20:40:02+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 最长相邻不相等子序列 II

给定一个字符串数组 words ，和一个数组 groups ，两个数组长度都是 n 。

两个长度相等字符串的 汉明距离 定义为对应位置字符 不同 的数目。

你需要从下标 [0, 1, ..., n - 1] 中选出一个 最长子序列 ，将这个子序列记作长度为 k 的 [iˇ0, iˇ1, ..., iˇ(k - 1)] ，它需要满足以下条件：

相邻 下标对应的 groups 值 不同。即，对于所有满足 0 < j + 1 < k 的 j 都有 groups[iˇj] != groups[iˇ(j + 1)] 。
对于所有 0 < j + 1 < k 的下标 j ，都满足 words[iˇj] 和 words[iˇ(j + 1)] 的长度 相等 ，且两个字符串之间的 汉明距离 为 1 。
请你返回一个字符串数组，它是下标子序列 依次 对应 words 数组中的字符串连接形成的字符串数组。如果有多个答案，返回任意一个。

子序列 指的是从原数组中删掉一些（也可能一个也不删掉）元素，剩余元素不改变相对位置得到的新的数组。

注意：words 中的字符串长度可能 不相等 。


```
impl Solution {
    pub fn get_words_in_longest_subsequence(words: Vec<String>, groups: Vec<i32>) -> Vec<String> {
        let n = words.len();
        // dp[i]: 以 words[i] 结尾的最长合法子序列长度
        // prev[i]: 该子序列中 words[i] 的前驱下标，-1 表示无前驱
        let mut dp = vec![1; n];
        let mut prev = vec![-1i32; n];
        let mut best = 0usize;

        for i in 0..n {
            for j in 0..i {
                // 相邻元素 groups 不同，且汉明距离为 1（长度相等）
                if groups[i] != groups[j] && dp[j] + 1 > dp[i] && Self::hamming_one(&words[i], &words[j]) {
                    dp[i] = dp[j] + 1;
                    prev[i] = j as i32;
                }
            }
            if dp[i] > dp[best] {
                best = i;
            }
        }

        // 从最优结尾回溯构造子序列
        let mut ans = Vec::with_capacity(dp[best]);
        let mut i = best as i32;
        while i >= 0 {
            ans.push(words[i as usize].clone());
            i = prev[i as usize];
        }
        ans.reverse();
        ans
    }

    /// 判断两个字符串长度相等且汉明距离恰好为 1
    #[inline]
    fn hamming_one(a: &str, b: &str) -> bool {
        let (a, b) = (a.as_bytes(), b.as_bytes());
        if a.len() != b.len() {
            return false;
        }
        let mut diff = 0;
        for i in 0..a.len() {
            if a[i] != b[i] {
                diff += 1;
                if diff > 1 {
                    return false;
                }
            }
        }
        diff == 1
    }
}
```
