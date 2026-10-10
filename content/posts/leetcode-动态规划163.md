---
title: "leetcode-动态规划163"
date: 2026-10-05T20:40:05+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 循环划分的最大得分

给你一个 循环 数组 nums 和一个整数 k。

将 nums 划分 为 最多 k 个子数组。由于 nums 是循环数组，这些子数组可以从数组末尾环绕回起点。

子数组的 范围 定义为其 最大值 与 最小值 的差值。划分的 得分 是所有子数组范围的总和。

返回所有循环划分方案中可能获得的 最大得分 。

子数组 是数组中的一个连续非空的元素序列。


```
impl Solution {
    /// 在循环数组 prices 的一段线性区间 [l, r) 上，完成至多 k+1 笔交易（含做空）的最大利润
    /// 使用股票 DP：f[j] = [free, hold_long, hold_short]
    fn maximum_profit(prices: &[i32], l: usize, r: usize, k: usize) -> i64 {
        let n = prices.len();
        const NEG: i64 = i64::MIN / 2;

        let mut f = vec![[NEG; 3]; k + 2];
        for j in 1..=k + 1 {
            f[j][0] = 0;
        }

        for i in l..r {
            let p = prices[i % n] as i64;
            // 从后往前更新，避免同一笔交易在同一天被重复使用
            for j in (1..=k + 1).rev() {
                let free = f[j][0];
                let long = f[j][1];
                let short = f[j][2];
                let prev_free = f[j - 1][0];

                f[j][0] = free.max(long + p).max(short - p);
                f[j][1] = long.max(prev_free - p);
                f[j][2] = short.max(prev_free + p);
            }
        }
        f[k + 1][0]
    }

    pub fn maximum_score(nums: Vec<i32>, k: i32) -> i64 {
        let n = nums.len();
        let k = k as usize;

        // 循环划分可以围绕最大值展开：最大值单独成段最有利
        // 两种切割方式：最大值作为第一个数，或作为最后一个数
        let max_i = nums
            .iter()
            .enumerate()
            .max_by_key(|&(_, &v)| v)
            .map(|(i, _)| i)
            .unwrap();

        let ans1 = Self::maximum_profit(&nums, max_i, max_i + n, k);
        let ans2 = Self::maximum_profit(&nums, max_i + 1, max_i + 1 + n, k);
        ans1.max(ans2)
    }
}
```
