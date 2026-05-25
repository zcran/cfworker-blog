---
title: "leetcode-记忆化27"
date: 2026-05-06T20:22:34+08:00
tags: ["leetcode", "记忆化"]
draft: false
---


## 卖木头块

给你两个整数 m 和 n ，分别表示一块矩形木块的高和宽。同时给你一个二维整数数组 prices ，其中 prices[i] = [hi, wi, pricei] 表示你可以以 pricei 元的价格卖一块高为 hi 宽为 wi 的矩形木块。

每一次操作中，你必须按下述方式之一执行切割操作，以得到两块更小的矩形木块：

沿垂直方向按高度 完全 切割木块，或
沿水平方向按宽度 完全 切割木块
在将一块木块切成若干小木块后，你可以根据 prices 卖木块。你可以卖多块同样尺寸的木块。你不需要将所有小木块都卖出去。你 不能 旋转切好后木块来交换它的高度值和宽度值。

请你返回切割一块大小为 m x n 的木块后，能得到的 最多 钱数。

注意你可以切割木块任意次。

```
use std::collections::HashMap;

impl Solution {
    /// 计算将一块 `m x n` 的木板切割后能得到的最大总价值（LeetCode 2312）
    ///
    /// 规则：
    /// - 可以沿水平或垂直方向将木板切成两块更小的木板。
    /// - 每块木板可以按给定价格表（尺寸 -> 价值）直接出售，也可以继续切割。
    /// - 目标：最大化最终所有木板的价值和。
    ///
    /// # 参数
    /// - `m`: 木板的高度
    /// - `n`: 木板的宽度
    /// - `prices`: 价格表，每个元素为 `[h, w, price]`，表示尺寸为 `h x w` 的木板售价
    ///
    /// # 返回
    /// 最大总价值（i64）
    pub fn selling_wood(m: i32, n: i32, prices: Vec<Vec<i32>>) -> i64 {
        // 转换为 usize 以简化索引访问，避免重复 as usize
        let m = m as usize;
        let n = n as usize;

        // 构建价格映射表 (高度, 宽度) -> 售价
        let mut price_map = HashMap::new();
        for price in prices {
            let h = price[0] as usize;
            let w = price[1] as usize;
            let val = price[2] as i64; // 售价转为 i64 避免后续计算溢出
            price_map.insert((h, w), val);
        }

        // 记忆化数组：dp[h][w] 表示尺寸为 h x w 的木板能获得的最大价值
        // 初始值 -1 表示未计算
        let mut dp = vec![vec![-1; n + 1]; m + 1];

        // 定义递归函数，使用闭包捕获 dp 和 price_map
        fn dfs(h: usize, w: usize, dp: &mut Vec<Vec<i64>>, price_map: &HashMap<(usize, usize), i64>) -> i64 {
            // 如果已经计算过，直接返回
            if dp[h][w] != -1 {
                return dp[h][w];
            }

            // 当前木板直接出售的价格（如果存在价格表条目，否则为 0）
            let direct_sell = *price_map.get(&(h, w)).unwrap_or(&0);

            // 尝试所有可能的水平切割（将高度切为 i 和 h-i）
            let best_horizontal = (1..h)
                .map(|i| dfs(i, w, dp, price_map) + dfs(h - i, w, dp, price_map))
                .max()
                .unwrap_or(0);

            // 尝试所有可能的垂直切割（将宽度切为 j 和 w-j）
            let best_vertical = (1..w)
                .map(|j| dfs(h, j, dp, price_map) + dfs(h, w - j, dp, price_map))
                .max()
                .unwrap_or(0);

            // 取三种方案的最大值
            let best = direct_sell.max(best_horizontal).max(best_vertical);

            // 记忆化存储并返回
            dp[h][w] = best;
            best
        }

        dfs(m, n, &mut dp, &price_map)
    }
}
```
