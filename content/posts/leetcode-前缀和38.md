---
title: "leetcode-前缀和38"
date: 2026-08-25T10:02:48+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 你能在你最喜欢的那天吃到你最喜欢的糖果吗？

给你一个下标从 0 开始的正整数数组 candiesCount ，其中 candiesCount[i] 表示你拥有的第 i 类糖果的数目。同时给你一个二维数组 queries ，其中 queries[i] = [favoriteTypei, favoriteDayi, dailyCapi] 。

你按照如下规则进行一场游戏：

你从第 0 天开始吃糖果。
你在吃完 所有 第 i - 1 类糖果之前，不能 吃任何一颗第 i 类糖果。
在吃完所有糖果之前，你必须每天 至少 吃 一颗 糖果。

请你构建一个布尔型数组 answer ，用以给出 queries 中每一项的对应答案。此数组满足：

answer.length == queries.length 。answer[i] 是 queries[i] 的答案。
answer[i] 为 true 的条件是：在每天吃 不超过 dailyCapi 颗糖果的前提下，你可以在第 favoriteDayi 天吃到第 favoriteTypei 类糖果；否则 answer[i] 为 false 。

注意，只要满足上面 3 条规则中的第二条规则，你就可以在同一天吃不同类型的糖果。

请你返回得到的数组 answer 。


```
impl Solution {
    /// 判断在给定条件下能否在第 favoriteDay 天吃到第 favoriteType 类糖果
    ///
    /// 条件：
    /// 1. 第 favoriteType 类糖果必须在所有前置糖果之后才能开始吃
    /// 2. 第 favoriteDay 天必须已经过了前置糖果的消耗时间
    /// 3. 每天最多吃 dailyCap 颗，需要保证在第 favoriteDay 天之前吃到该类型
    ///
    /// 判断逻辑：
    /// - 最早能开始吃第 f_type 类糖果的天数：prefix[f_type] / dailyCap（每天吃最多）
    /// - 最晚必须开始吃第 f_type 类糖果的天数：prefix[f_type+1] - 1（在此之前必须吃完）
    /// - 需要在第 f_day 天能吃到，即 f_day 在 [earliest_day, latest_day] 范围内
    pub fn can_eat(candies_count: Vec<i32>, queries: Vec<Vec<i32>>) -> Vec<bool> {
        let n = candies_count.len();

        // 前缀和：prefix[i] 表示前 i 类糖果的总数
        let mut prefix = vec![0i64; n + 1];
        for (i, &cnt) in candies_count.iter().enumerate() {
            prefix[i + 1] = prefix[i] + cnt as i64;
        }

        let mut ans = Vec::with_capacity(queries.len());

        for query in queries {
            let f_type = query[0] as usize;
            let f_day = query[1] as i64;
            let daily_cap = query[2] as i64;

            // 第 f_type 类糖果的起始位置和结束位置（前缀和索引）
            let before = prefix[f_type];      // 该类型之前的糖果总数
            let total = prefix[f_type + 1];   // 包含该类型在内的糖果总数

            // 最早能在第几天吃到该类糖果：至少要吃光前面的所有糖果
            // 每天吃 daily_cap 颗，最少需要 ceil(before / daily_cap) 天
            let earliest_day = before / daily_cap;

            // 最晚能在第几天吃到该类糖果：该类型糖果的最后一天
            // 必须在第 total - 1 颗糖果的当天吃到（0-indexed 天数）
            let latest_day = total - 1;

            // 第 f_day 天必须落在 [earliest_day, latest_day] 区间内
            ans.push(f_day >= earliest_day && f_day <= latest_day);
        }

        ans
    }
}
```
