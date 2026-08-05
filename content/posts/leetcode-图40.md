---
title: "leetcode-图40"
date: 2026-07-27T10:01:37+08:00
tags: ["leetcode", "图"]
draft: false
---


## 两天自由外汇交易后的最大货币数

给你一个字符串 initialCurrency，表示初始货币类型，并且你一开始拥有 1.0 单位的 initialCurrency。

另给你四个数组，分别表示货币对（字符串）和汇率（实数）：

· pairs1[i] = [startCurrencyi, targetCurrencyi] 表示在 第 1 天，可以按照汇率 rates1[i] 将 startCurrencyi 转换为 targetCurrencyi。
· pairs2[i] = [startCurrencyi, targetCurrencyi] 表示在 第 2 天，可以按照汇率 rates2[i] 将 startCurrencyi 转换为 targetCurrencyi。
· 此外，每种 targetCurrency 都可以以汇率 1 / rate 转换回对应的 startCurrency。

你可以在 第 1 天 使用 rates1 进行任意次数的兑换（包括 0 次），然后在 第 2 天 使用 rates2 再进行任意次数的兑换（包括 0 次）。

返回在两天兑换后，最大可能拥有的 initialCurrency 的数量。

注意：汇率是有效的，并且第 1 天和第 2 天的汇率之间相互独立，不会产生矛盾。


```
use std::collections::HashMap;

impl Solution {
    pub fn max_amount(
        initial_currency: String,
        pairs1: Vec<Vec<String>>,
        rates1: Vec<f64>,
        pairs2: Vec<Vec<String>>,
        rates2: Vec<f64>,
    ) -> f64 {
        // 计算第1天兑换后，每种货币能换得的初始货币数量
        let day1_amount = Self::calc_amount(&initial_currency, &pairs1, &rates1);
        // 计算第2天兑换关系：从初始货币角度，1单位货币能换得多少其他货币
        let day2_amount = Self::calc_amount(&initial_currency, &pairs2, &rates2);

        // 对于第2天能换到的每种货币，计算：第1天能换到的数量 / 第2天需要消耗的数量
        // 即 maximize(day1_amount[x] / day2_amount[x])
        day2_amount
            .iter()
            .map(|(currency, rate)| day1_amount.get(currency).unwrap_or(&0.0) / rate)
            .fold(0.0, f64::max)
    }

    /// 计算从初始货币出发，通过兑换图能到达的所有货币及其兑换比例
    /// 返回 HashMap<货币, 1单位初始货币能兑换到的数量>
    fn calc_amount(
        initial: &str,
        pairs: &[Vec<String>],
        rates: &[f64],
    ) -> HashMap<String, f64> {
        // 构建双向兑换图
        let mut graph: HashMap<&str, Vec<(&str, f64)>> = HashMap::new();
        for (pair, &rate) in pairs.iter().zip(rates) {
            let from = &pair[0];
            let to = &pair[1];
            graph.entry(from).or_default().push((to, rate));
            graph.entry(to).or_default().push((from, 1.0 / rate));
        }

        // DFS 计算所有可达货币的兑换比例
        let mut amount = HashMap::new();
        Self::dfs(initial, 1.0, &graph, &mut amount);
        amount
    }

    /// DFS 遍历兑换图，计算每种货币的兑换比例
    fn dfs(
        currency: &str,
        cur_amount: f64,
        graph: &HashMap<&str, Vec<(&str, f64)>>,
        amount: &mut HashMap<String, f64>,
    ) {
        amount.insert(currency.to_string(), cur_amount);

        if let Some(neighbors) = graph.get(currency) {
            for &(next, rate) in neighbors {
                if !amount.contains_key(next) {
                    Self::dfs(next, cur_amount * rate, graph, amount);
                }
            }
        }
    }
}
```
