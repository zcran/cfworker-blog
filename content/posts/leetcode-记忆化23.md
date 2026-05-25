---
title: "leetcode-记忆化23"
date: 2026-05-06T20:22:34+08:00
tags: ["leetcode", "记忆化"]
draft: false
---


## 得到新鲜甜甜圈的最多组数

有一个甜甜圈商店，每批次都烤 batchSize 个甜甜圈。这个店铺有个规则，就是在烤一批新的甜甜圈时，之前 所有 甜甜圈都必须已经全部销售完毕。给你一个整数 batchSize 和一个整数数组 groups ，数组中的每个整数都代表一批前来购买甜甜圈的顾客，其中 groups[i] 表示这一批顾客的人数。每一位顾客都恰好只要一个甜甜圈。

当有一批顾客来到商店时，他们所有人都必须在下一批顾客来之前购买完甜甜圈。如果一批顾客中第一位顾客得到的甜甜圈不是上一组剩下的，那么这一组人都会很开心。

你可以随意安排每批顾客到来的顺序。请你返回在此前提下，最多 有多少组人会感到开心。

```
impl Solution {
    /// 计算最多可以有多少组“新鲜甜甜圈”组（LeetCode 1815）
    /// 思路：
    /// 1. 将每个组的大小按 `batch_size` 取模分组，余数为 0 的组直接可以独立成组。
    /// 2. 对于余数 1..batch_size-1，使用状态压缩 DFS 来决定选取顺序。
    ///    状态掩码用 5 位二进制存储每个余数的剩余数量（假设每个余数的数量 ≤31）。
    ///    每步选择一个余数 i，如果选取后剩余组的累计总大小能被 batch_size 整除，则答案 +1。
    /// 3. 记忆化搜索避免重复计算。
    pub fn max_happy_groups(batch_size: i32, groups: Vec<i32>) -> i32 {
        // 每个余数使用 5 位二进制存储（最多表示 31，算法假设数量在此范围内）
        const BITS_PER_REMAINDER: usize = 5;
        const REMAINDER_MASK: i64 = (1 << BITS_PER_REMAINDER) - 1;

        let batch = batch_size as usize;          // 批次大小，转为 usize 方便索引
        // 统计每个余数的组数
        let mut cnt = vec![0; batch];
        for g in groups {
            cnt[(g % batch_size) as usize] += 1;
        }

        // 余数为 0 的组直接贡献 happy（每组单独成组）
        let base_happy = cnt[0] as i32;

        // 如果 batch <= 1，所有余数均为 0，无需进一步处理
        if batch <= 1 {
            return base_happy;
        }

        // 构建初始状态掩码
        // 对于每个余数 i (1..batch)，其数量存储在掩码的偏移 (i-1)*BITS_PER_REMAINDER 处
        let mut start_mask = 0i64;
        for i in 1..batch {
            let shift = (i - 1) * BITS_PER_REMAINDER;
            start_mask |= (cnt[i] as i64) << shift;
        }

        use std::collections::HashMap;
        let mut memo = HashMap::new();
        memo.insert(0i64, 0); // 所有余数数量为 0 时，额外 happy = 0

        /// 深度优先搜索 + 记忆化
        /// - `memo`: 记忆化表，记录每个掩码对应的最佳额外 happy 数
        /// - `batch`: 批次大小（usize）
        /// - `mask`: 当前剩余组的状态掩码
        fn dfs(memo: &mut HashMap<i64, i32>, batch: usize, mask: i64) -> i32 {
            if let Some(&val) = memo.get(&mask) {
                return val;
            }

            // 计算当前剩余组的总大小（未取模，直接求和）
            let mut total = 0i64;
            for i in 1..batch {
                let shift = (i - 1) * BITS_PER_REMAINDER;
                let amount = (mask >> shift) & REMAINDER_MASK;
                total += (i as i64) * amount;
            }

            let mut best = 0;
            // 尝试每一种余数，若还有剩余则选取一个
            for i in 1..batch {
                let shift = (i - 1) * BITS_PER_REMAINDER;
                let amount = (mask >> shift) & REMAINDER_MASK;
                if amount > 0 {
                    // 移除一个余数为 i 的组，得到新状态
                    let new_mask = mask - (1i64 << shift);
                    let mut result = dfs(memo, batch, new_mask);
                    // 如果移除该组后剩余组的总大小能被 batch 整除，则当前选择的这一组可以形成一个完整的 happy 组
                    if (total - i as i64) % batch as i64 == 0 {
                        result += 1;
                    }
                    best = best.max(result);
                }
            }

            memo.insert(mask, best);
            best
        }

        let extra_happy = dfs(&mut memo, batch, start_mask);
        base_happy + extra_happy
    }
}
```
