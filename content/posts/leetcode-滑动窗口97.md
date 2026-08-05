---
title: "leetcode-滑动窗口97"
date: 2026-07-18T11:02:34+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 统计重新排列后包含另一个字符串的子字符串数目 II

给你两个字符串 word1 和 word2 。

如果一个字符串 x 重新排列后，word2 是重排字符串的 前缀 ，那么我们称字符串 x 是 合法的 。

请你返回 word1 中 合法 子字符串 的数目。

注意 ，这个问题中的内存限制比其他题目要 小 ，所以你 必须 实现一个线性复杂度的解法。




```
impl Solution {
    pub fn valid_substring_count(word1: String, word2: String) -> i64 {
        let word1 = word1.as_bytes();
        let n = word1.len();

        // diff[c] 表示窗口内字符 c 的数量相对于需求的差值
        // 负值表示该字符仍缺少，正值表示有多余
        let mut diff = [0; 26];
        for &ch in word2.as_bytes() {
            diff[(ch - b'a') as usize] -= 1;
        }

        // cnt 记录当前仍然缺少的字符种类数（即 diff[c] < 0 的数量）
        let mut missing = diff.iter().filter(|&&c| c < 0).count();

        let mut ans = 0i64;
        let mut left = 0;

        // 滑动窗口：右指针扩展
        for right in 0..n {
            // 1. 添加右端字符到窗口
            let ch = (word1[right] - b'a') as usize;
            diff[ch] += 1;

            // 如果该字符从缺少变为满足（diff 从 -1 变为 0），减少 missing 计数
            if diff[ch] == 0 {
                missing -= 1;
            }

            // 2. 窗口满足条件时，尝试收缩左指针
            while missing == 0 {
                // 当前窗口 [left..=right] 合法
                // 以 left 为起点，右端点可以是 right..n-1，共 (n - right) 个合法子串
                ans += (n - right) as i64;

                // 移除左端字符
                let left_ch = (word1[left] - b'a') as usize;
                diff[left_ch] -= 1;

                // 如果该字符从满足变为缺少（diff 从 0 变为 -1），增加 missing 计数
                if diff[left_ch] == -1 {
                    missing += 1;
                }

                left += 1;
            }
        }

        ans
    }
}
```
