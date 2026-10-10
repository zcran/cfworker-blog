---
title: "leetcode-动态规划78"
date: 2026-10-05T20:39:59+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 工作计划的最低难度

你需要制定一份 d 天的工作计划表。工作之间存在依赖，要想执行第 i 项工作，你必须完成全部 j 项工作（ 0 <= j < i）。

你每天 至少 需要完成一项任务。工作计划的总难度是这 d 天每一天的难度之和，而一天的工作难度是当天应该完成工作的最大难度。

给你一个整数数组 jobDifficulty 和一个整数 d，分别代表工作难度和需要计划的天数。第 i 项工作的难度是 jobDifficulty[i]。

返回整个工作计划的 最小难度 。如果无法制定工作计划，则返回 -1 。


```
impl Solution {
    /// 1335. 工作计划的最低难度
    ///
    /// dp[i][j]：前 j+1 项工作安排到第 i 天（共 i+1 天）的最小总难度。
    /// 转移：枚举最后一天的起始工作 k：
    ///   dp[i][j] = min_{k} ( dp[i-1][k-1] + max(job[k..=j]) )
    ///
    /// 直接做是 O(d·n^2)。优化：固定 j，随 k 从 j 递减到 i，维护后缀最大值，
    /// 并用单调栈维护 dp[i-1][k-1] 的分段最小值，使每层摊销 O(n)。
    ///
    /// 这里保留官方题解风格的单调栈实现，逻辑不变但做了以下整理：
    ///   - 用 usize、i32 明确类型，去掉多余转换
    ///   - 栈元素 (idx, val) 表示「从 idx 开始的一段中，dp[i-1][k-1] 的最小值为 val」
    ///   - dp 只保留当前层与上一层，空间从 O(d·n) 降到 O(n)
    pub fn min_difficulty(job_difficulty: Vec<i32>, d: i32) -> i32 {
        let n = job_difficulty.len();
        let d = d as usize;
        // 每天至少一项工作，天数不能超过工作数
        if n < d {
            return -1;
        }

        // prev[j]：前 j+1 项工作分到 i-1 天的最小难度
        let mut prev = vec![0i32; n];
        prev[0] = job_difficulty[0];
        for j in 1..n {
            prev[j] = prev[j - 1].max(job_difficulty[j]);
        }

        let mut cur = vec![0i32; n];

        for i in 1..d {
            // 单调栈：(起始下标, 该段中 prev[k-1] 的最小值)
            let mut stack: Vec<(usize, i32)> = Vec::with_capacity(n);

            for j in i..n {
                let jd = job_difficulty[j];
                // 当前最后一天至少包含工作 j，其难度后缀最大值从 jd 开始
                let mut best_prefix = prev[j - 1];

                // 弹出难度不超过 jd 的工作，合并它们对应的前缀最小值
                while let Some(&(idx, val)) = stack.last() {
                    if job_difficulty[idx] <= jd {
                        stack.pop();
                        best_prefix = best_prefix.min(val);
                    } else {
                        break;
                    }
                }

                // 方案 A：最后一天从 j 开始（只含 j），前缀为 prev[j-1] 及合并后的最小值
                cur[j] = best_prefix + jd;

                // 方案 B：最后一天从栈顶的 idx 开始，此时后缀最大值仍由栈顶对应段决定
                if let Some(&(m, _)) = stack.last() {
                    cur[j] = cur[j].min(cur[m]);
                }

                stack.push((j, best_prefix));
            }

            std::mem::swap(&mut prev, &mut cur);
        }

        prev[n - 1]
    }
}
```
