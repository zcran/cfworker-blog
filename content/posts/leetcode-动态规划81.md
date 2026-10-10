---
title: "leetcode-动态规划81"
date: 2026-10-05T20:39:59+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 数位成本和为目标值的最大数字

给你一个整数数组 cost 和一个整数 target 。请你返回满足如下规则可以得到的 最大 整数：

给当前结果添加一个数位（i + 1）的成本为 cost[i] （cost 数组下标从 0 开始）。
总成本必须恰好等于 target 。
添加的数位中没有数字 0 。

由于答案可能会很大，请你以字符串形式返回。

如果按照上述要求无法得到任何整数，请你返回 "0" 。


```
impl Solution {
    pub fn largest_number(cost: Vec<i32>, target: i32) -> String {
        let target = target as usize;
        // dp[j] 表示成本恰好为 j 时能得到的最大位数；i32::MIN 表示该成本无法恰好凑出
        let mut dp = vec![i32::MIN; target + 1];
        dp[0] = 0;

        // 完全背包：先遍历物品再遍历容量，保证每个数位可重复使用
        for &c in &cost {
            let c = c as usize;
            for j in c..=target {
                if dp[j - c] != i32::MIN {
                    dp[j] = dp[j].max(dp[j - c] + 1);
                }
            }
        }

        if dp[target] < 0 {
            return "0".to_string();
        }

        // 贪心构造：从高位数字 9 到 1，尽量多地放置当前数字
        let mut ans = String::with_capacity(dp[target] as usize);
        let mut j = target;
        for i in (0..9).rev() {
            let c = cost[i] as usize;
            while j >= c && dp[j] == dp[j - c] + 1 {
                ans.push((b'1' + i as u8) as char);
                j -= c;
            }
        }
        ans
    }
}
```
