---
title: "leetcode-记忆化10"
date: 2026-05-06T20:22:33+08:00
tags: ["leetcode", "记忆化"]
draft: false
---


## 贴纸拼词

我们有 n 种不同的贴纸。每个贴纸上都有一个小写的英文单词。

您想要拼写出给定的字符串 target ，方法是从收集的贴纸中切割单个字母并重新排列它们。如果你愿意，你可以多次使用每个贴纸，每个贴纸的数量是无限的。

返回你需要拼出 target 的最小贴纸数量。如果任务不可能，则返回 -1 。

注意：在所有的测试用例中，所有的单词都是从 1000 个最常见的美国英语单词中随机选择的，并且 target 被选择为两个随机单词的连接。

```
impl Solution {
    /// 贴纸拼词问题 (LeetCode 691)
    ///
    /// 给定一组贴纸（每个贴纸可无限使用），每种贴纸上包含若干字母。
    /// 目标是拼出目标单词 `target`，求最少需要的贴纸数量。
    /// 如果无法拼出，返回 -1。
    pub fn min_stickers(stickers: Vec<String>, target: String) -> i32 {
        let target_bytes = target.as_bytes();
        let n = target_bytes.len();
        let total_masks = 1 << n; // 所有子集的位掩码总数

        // ---------- 1. 预处理贴纸：转换为字符计数数组 ----------
        let sticker_counts: Vec<[usize; 26]> = stickers
            .iter()
            .map(|s| {
                let mut cnt = [0; 26];
                for &b in s.as_bytes() {
                    cnt[(b - b'a') as usize] += 1;
                }
                cnt
            })
            .collect();

        // ---------- 2. DP 数组：dp[mask] = 拼出 mask 所需最少贴纸数 ----------
        // 初始化为 -1 表示未计算，dp[0] = 0
        let mut dp = vec![-1; total_masks];
        dp[0] = 0;

        // ---------- 3. 深度优先搜索（递归 + 记忆化）----------
        fn dfs(
            mask: usize,
            target: &[u8],
            sticker_counts: &[[usize; 26]],
            dp: &mut [i32],
        ) -> i32 {
            if dp[mask] != -1 {
                return dp[mask];
            }

            // 初始化为一个较大的值（避免溢出）
            let mut best = i32::MAX / 2;

            for counts in sticker_counts {
                let remaining = apply_sticker(mask, target, counts);
                // 如果贴纸没有产生任何变化，跳过（避免死循环）
                if remaining != mask {
                    let candidate = 1 + dfs(remaining, target, sticker_counts, dp);
                    if candidate < best {
                        best = candidate;
                    }
                }
            }

            dp[mask] = best;
            best
        }

        // 辅助函数：使用一个贴纸后，剩余的需要覆盖的位掩码
        fn apply_sticker(mut mask: usize, target: &[u8], sticker: &[usize; 26]) -> usize {
            let mut available = *sticker; // 拷贝当前贴纸的字母计数
            for (i, &ch) in target.iter().enumerate() {
                // 如果当前位还未被覆盖，且贴纸中还有该字母
                if (mask >> i) & 1 == 1 {
                    let idx = (ch - b'a') as usize;
                    if available[idx] > 0 {
                        available[idx] -= 1;
                        mask &= !(1 << i); // 清除该位
                    }
                }
            }
            mask
        }

        let full_mask = (1 << n) - 1;
        let ans = dfs(full_mask, target_bytes, &sticker_counts, &mut dp);
        if ans >= i32::MAX / 2 { -1 } else { ans }
    }
}
```
