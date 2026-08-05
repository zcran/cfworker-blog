---
title: "leetcode-滑动窗口110"
date: 2026-07-18T11:02:35+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 按策略买卖股票的最佳时机

给你两个整数数组 prices 和 strategy，其中：

· prices[i] 表示第 i 天某股票的价格。
· strategy[i] 表示第 i 天的交易策略，其中：
    · -1 表示买入一单位股票。
    · 0 表示持有股票。
    · 1 表示卖出一单位股票。

同时给你一个 偶数 整数 k，你可以对 strategy 进行 最多一次 修改。一次修改包括：

· 选择 strategy 中恰好 k 个 连续 元素。
· 将前 k / 2 个元素设为 0（持有）。
· 将后 k / 2 个元素设为 1（卖出）。

利润 定义为所有天数中 strategy[i] * prices[i] 的 总和 。

返回你可以获得的 最大 可能利润。

注意： 没有预算或股票持有数量的限制，因此所有买入和卖出操作均可行，无需考虑过去的操作。


```
impl Solution {
    pub fn max_profit(prices: Vec<i32>, strategy: Vec<i32>, k: i32) -> i64 {
        let n = prices.len();
        let k = k as usize;
        let half = k / 2;

        // 前缀和：策略利润 和 价格总和
        let mut profit_pref = vec![0i64; n + 1];
        let mut price_pref = vec![0i64; n + 1];
        for i in 0..n {
            profit_pref[i + 1] = profit_pref[i] + prices[i] as i64 * strategy[i] as i64;
            price_pref[i + 1] = price_pref[i] + prices[i] as i64;
        }

        let total_profit = profit_pref[n];
        let mut max_profit = total_profit;

        // 枚举修改区间的右端点 i（区间为 [i-k+1, i]）
        // 将前 half 个改为持有(0)，后 half 个改为卖出(1)
        for end in k - 1..n {
            let start = end - k + 1;
            let mid = start + half;  // 前 half 个的结束位置

            // 不变部分：左侧 [0, start-1] + 右侧 [end+1, n-1]
            let left_profit = profit_pref[start];
            let right_profit = total_profit - profit_pref[end + 1];

            // 修改部分：前 half 个利润变为 0，后 half 个利润变为价格总和
            let changed_profit = price_pref[end + 1] - price_pref[mid];

            let new_profit = left_profit + changed_profit + right_profit;
            max_profit = max_profit.max(new_profit);
        }

        max_profit
    }
}
```
