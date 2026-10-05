---
title: "leetcode-贪心77"
date: 2026-10-03T12:55:39+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 合并若干三元组以形成目标三元组

三元组 是一个由三个整数组成的数组。给你一个二维整数数组 triplets ，其中 triplets[i] = [ai, bi, ci] 表示第 i 个 三元组 。同时，给你一个整数数组 target = [x, y, z] ，表示你想要得到的 三元组 。

为了得到 target ，你需要对 triplets 执行下面的操作 任意次（可能 零 次）：

选出两个下标（下标 从 0 开始 计数）i 和 j（i != j），并 更新 triplets[j] 为 [max(ai, aj), max(bi, bj), max(ci, cj)] 。

例如，triplets[i] = [2, 5, 3] 且 triplets[j] = [1, 7, 5]，triplets[j] 将会更新为 [max(2, 1), max(5, 7), max(3, 5)] = [2, 7, 5] 。

如果通过以上操作我们可以使得目标 三元组 target 成为 triplets 的一个 元素 ，则返回 true ；否则，返回 false 。


```
impl Solution {
    pub fn merge_triplets(triplets: Vec<Vec<i32>>, target: Vec<i32>) -> bool {
        // 只保留每个维度都不超过 target 的三元组，合并（各维取最大值）
        // 最终若能恰好等于 target，则说明可以构造出 target
        let mut merged = [i32::MIN; 3];
        for t in &triplets {
            if t[0] <= target[0] && t[1] <= target[1] && t[2] <= target[2] {
                merged[0] = merged[0].max(t[0]);
                merged[1] = merged[1].max(t[1]);
                merged[2] = merged[2].max(t[2]);
            }
        }
        merged == [target[0], target[1], target[2]]
    }
}
```
