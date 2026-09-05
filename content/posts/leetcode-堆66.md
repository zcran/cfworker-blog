---
title: "leetcode-堆66"
date: 2026-08-21T09:54:14+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 选择 K 个任务的最大总分数

给你两个整数数组 technique1 和 technique2，长度均为 n，其中 n 代表需要完成的任务数量。

如果第 i 个任务使用技巧 1 完成，你将获得 technique1[i] 分。
如果使用技巧 2 完成，你将获得 technique2[i] 分。

此外给你一个整数 k，表示 必须 使用技巧 1 完成的 最少 任务数量。

你 必须 使用技巧 1 完成 至少 k 个任务（不需要是前 k 个任务）。

剩余的任务可以使用 任一 技巧完成。

返回一个整数，表示你能获得的 最大总分数。




```
impl Solution {
    /// 返回能获得的最大总分数。
    ///
    /// 策略：
    /// 1. 先假设所有 n 个任务都用技巧 1，得到基础分 sum(technique1)。
    /// 2. 对每个任务，计算换成技巧 2 的额外收益：diff = technique2[i] - technique1[i]。
    ///    若 diff > 0，则换成技巧 2 更划算，将其收集到 diff 数组中。
    /// 3. 由于必须至少用技巧 1 完成 k 个任务，最多只能将 (n - k) 个任务换成技巧 2。
    /// 4. 将 diff 数组按降序排序，取前 limit 个最大的正收益加到总分中。
    ///
    /// 时间复杂度：O(n log n)，主要来自排序（diff 最多 n 个元素）。
    /// 空间复杂度：O(n)，用于存储正收益差值。
    pub fn max_points(technique1: Vec<i32>, technique2: Vec<i32>, k: i32) -> i64 {
        let n = technique1.len();
        let k = k as usize;

        // 步骤 1：所有任务先用技巧 1，同时收集正收益差值
        let mut total: i64 = 0;
        let mut diffs: Vec<i32> = Vec::with_capacity(n);

        for i in 0..n {
            total += technique1[i] as i64;
            let d = technique2[i] - technique1[i];
            if d > 0 {
                diffs.push(d);
            }
        }

        // 步骤 2：按收益从高到低排序
        diffs.sort_unstable_by(|a, b| b.cmp(a));

        // 步骤 3：最多换 (n - k) 个，取前 limit 个最大正收益
        let limit = (n - k).min(diffs.len());
        for i in 0..limit {
            total += diffs[i] as i64;
        }

        total
    }
}
```
