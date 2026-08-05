---
title: "leetcode-回溯51"
date: 2026-07-04T10:22:04+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 将字符串拆分为递减的连续值

给你一个仅由数字组成的字符串 s 。

请你判断能否将 s 拆分成两个或者多个 非空子字符串 ，使子字符串的 数值 按 降序 排列，且每两个 相邻子字符串 的数值之 差 等于 1 。

· 例如，字符串 s = "0090089" 可以拆分成 ["0090", "089"] ，数值为 [90,89] 。这些数值满足按降序排列，且相邻值相差 1 ，这种拆分方法可行。
· 另一个例子中，字符串 s = "001" 可以拆分成 ["0", "01"]、["00", "1"] 或 ["0", "0", "1"] 。然而，所有这些拆分方法都不可行，因为对应数值分别是 [0,1]、[0,1] 和 [0,0,1] ，都不满足按降序排列的要求。

如果可以按要求拆分 s ，返回 true ；否则，返回 false 。

子字符串 是字符串中的一个连续字符序列。


```
impl Solution {
    pub fn split_string(s: String) -> bool {
        let s = s.as_bytes();
        let n = s.len();

        // dp[i][j] 表示子串 s[i..=j] 的数值，-1 表示未计算
        let mut dp = vec![vec![-1; n]; n];

        // 回溯搜索
        Self::backtrack(&mut dp, &s, 0, 0)
    }

    /// 回溯搜索拆分方案
    /// - dp: 缓存子串数值
    /// - s: 原始字符串的字节数组
    /// - start: 当前子串的起始位置
    /// - prev: 前一个子串的数值（用于比较，第一个子串 prev=0 表示无限制）
    fn backtrack(dp: &mut Vec<Vec<i64>>, s: &[u8], start: usize, prev: i64) -> bool {
        // 已处理完所有字符，拆分成功
        if start >= dp.len() {
            return true;
        }

        // 初始化当前起始位置的单个字符数值
        dp[start][start] = (s[start] - b'0') as i64;

        // 如果是第一个子串（无前驱限制）
        if start == 0 {
            // 枚举第一个子串的所有可能长度，但必须至少留一个字符给第二个子串
            for end in start..dp.len() - 1 {
                // 计算子串 s[start..=end] 的数值（使用缓存避免重复计算）
                if dp[start][end] == -1 {
                    dp[start][end] = dp[start][end - 1] * 10 + (s[end] - b'0') as i64;
                }
                // 递归搜索剩余部分
                if Self::backtrack(dp, s, end + 1, dp[start][end]) {
                    return true;
                }
            }
        } else {
            // 非第一个子串，必须满足降序且差为 1
            for end in start..dp.len() {
                // 计算当前子串数值
                if dp[start][end] == -1 {
                    dp[start][end] = dp[start][end - 1] * 10 + (s[end] - b'0') as i64;
                }

                let curr = dp[start][end];

                // 当前数值必须小于前一个数值
                if curr >= prev {
                    break; // 因为数值随长度增加而增大，后续只会更大，可以提前终止
                }

                // 检查是否满足差值为 1
                if curr == prev - 1 {
                    if Self::backtrack(dp, s, end + 1, curr) {
                        return true;
                    }
                }
                // 如果 curr < prev - 1，继续增加长度以增大数值
                // 如果 curr > prev - 1，由于数值随长度增加单调递增，后续不可能再等于 prev - 1
                // 但我们已经通过 curr >= prev 提前 break，所以这里无需额外处理
            }
        }
        false
    }
}
```
