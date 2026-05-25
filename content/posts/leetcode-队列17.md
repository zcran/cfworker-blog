---
title: "leetcode-队列17"
date: 2026-05-23T09:21:37+08:00
tags: ["leetcode", "队列"]
draft: false
---


## 划分数组得到最小的值之和


给你两个数组 nums 和 andValues，长度分别为 n 和 m。

数组的 值 等于该数组的 最后一个 元素。

你需要将 nums 划分为 m 个 不相交的连续 子数组，对于第 i 个子数组 [li, ri]，子数组元素的按位 AND 运算结果等于 andValues[i]，换句话说，对所有的 1 <= i <= m，nums[li] & nums[li + 1] & ... & nums[ri] == andValues[i] ，其中 & 表示按位 AND 运算符。

返回将 nums 划分为 m 个子数组所能得到的可能的 最小 子数组 值 之和。如果无法完成这样的划分，则返回 -1 。


```
use std::collections::HashMap;

const INF: i32 = (1 << 20) - 1;

impl Solution {
    /// 将 nums 分成 m 段，第 j 段的 AND 值必须等于 and_values[j]
    /// 求所有段最后一个元素之和的最小值，若不可能则返回 -1
    pub fn minimum_value_sum(nums: Vec<i32>, and_values: Vec<i32>) -> i32 {
        let n = nums.len();
        let m = and_values.len();
        // 单张哈希表，键为 (i, j, cur) 打包成的 u64
        let mut memo = HashMap::new();
        let res = Self::dfs(0, 0, INF, &nums, &and_values, &mut memo);
        if res < INF { res } else { -1 }
    }

    fn dfs(i: usize, j: usize, cur: i32, nums: &[i32], and_vals: &[i32], memo: &mut HashMap<u64, i32>) -> i32 {
        let n = nums.len();
        let m = and_vals.len();

        // 边界条件
        if i == n && j == m { return 0; }          // 完全匹配
        if i == n || j == m { return INF; }        // 剩余无法匹配

        // 剪枝：当前 cur 已无法满足后续 and_vals[j]
        if (cur & and_vals[j]) != and_vals[j] {
            return INF;
        }

        // 打包键值 (i, j, cur) -> u64
        let key = ((i as u64) << 40) | ((j as u64) << 20) | (cur as u64 & 0xFFFFF);
        if let Some(&v) = memo.get(&key) {
            return v;
        }

        let new_cur = cur & nums[i];
        // 若新 cur 无法满足当前段目标，提前返回
        if (new_cur & and_vals[j]) != and_vals[j] {
            memo.insert(key, INF);
            return INF;
        }

        // 不切分：继续扩展当前段
        let mut res = Self::dfs(i + 1, j, new_cur, nums, and_vals, memo);

        // 切分：当前段恰好满足 and_vals[j]
        if new_cur == and_vals[j] {
            let split = Self::dfs(i + 1, j + 1, INF, nums, and_vals, memo);
            if split < INF {
                res = res.min(split + nums[i]);
            }
        }

        memo.insert(key, res);
        res
    }
}
```
