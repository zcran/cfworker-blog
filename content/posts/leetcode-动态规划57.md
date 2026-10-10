---
title: "leetcode-动态规划57"
date: 2026-10-05T20:39:57+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 删列造序 III

给定由 n 个小写字母字符串组成的数组 strs ，其中每个字符串长度相等。

选取一个删除索引序列，对于 strs 中的每个字符串，删除对应每个索引处的字符。

比如，有 strs = ["abcdef","uvwxyz"] ，删除索引序列 {0, 2, 3} ，删除后为 ["bef", "vyz"] 。

假设，我们选择了一组删除索引 answer ，那么在执行删除操作之后，最终得到的数组的行中的 每个元素 都是按字典序排列的（即 (strs[0][0] <= strs[0][1] <= ... <= strs[0][strs[0].length - 1]) 和 (strs[1][0] <= strs[1][1] <= ... <= strs[1][strs[1].length - 1]) ，依此类推）。

请返回 answer.length 的最小可能值 。


```
impl Solution {
    /// 960. 删列造序 III
    ///
    /// 问题等价于：在所有列中选出最多的列，使这些列构成"字典序递增"序列，
    /// 则最少删除数 = 总列数 - 最多保留列数。
    ///
    /// 对任意两列 i < j，若对所有行都满足 strs[r][i] <= strs[r][j]，
    /// 则列 i 可以接在列 j 之后（保留的列按原顺序排列，故倒序遍历）。
    ///
    /// dp[i]：以第 i 列作为保留序列最后一列时，能保留的最大列数。
    /// 转移：dp[i] = max(1 + dp[j])，其中 j > i 且列 i 可放在列 j 之前。
    /// 用字节比较代替 chars().nth()，避免逐字符重新遍历；列的字符可预先转成 bytes。
    pub fn min_deletion_size(strs: Vec<String>) -> i32 {
        let n = strs[0].len();
        // 预先把每行转成字节切片，随机访问 O(1)
        let rows: Vec<&[u8]> = strs.iter().map(|s| s.as_bytes()).collect();

        let mut dp = vec![1i32; n];
        // 倒序遍历，保证计算 dp[i] 时所有 dp[j] (j > i) 已就绪
        for i in (0..n.saturating_sub(1)).rev() {
            for j in i + 1..n {
                // 检查列 i 是否逐行 <= 列 j
                if rows.iter().all(|r| r[i] <= r[j]) {
                    dp[i] = dp[i].max(1 + dp[j]);
                }
            }
        }

        n as i32 - dp.iter().copied().max().unwrap_or(1)
    }
}
```
