---
title: "leetcode-动态规划157"
date: 2026-10-05T20:40:05+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 硬币面值还原

给你一个 从 1 开始计数 的整数数组 numWays，其中 numWays[i] 表示使用某些 固定 面值的硬币（每种面值可以使用无限次）凑出总金额 i 的方法数。每种面值都是一个 正整数 ，并且其值 最多 为 numWays.length。

然而，具体的硬币面值已经 丢失 。你的任务是还原出可能生成这个 numWays 数组的面值集合。

返回一个按从小到大顺序排列的数组，其中包含所有可能的 唯一 整数面值。

如果不存在这样的集合，返回一个 空 数组。


```
impl Solution {
    pub fn find_coins(num_ways: Vec<i32>) -> Vec<i32> {
        let n = num_ways.len();
        // f[j]：用已确定的硬币面值凑出金额 j 的方案数
        let mut f = vec![0i64; n + 1];
        f[0] = 1;
        let mut ans = Vec::new();

        for i in 1..=n {
            let ways = num_ways[i - 1] as i64;
            if ways == f[i] {
                // 当前方案数已与目标一致，说明面值 i 不是硬币
                continue;
            }
            if ways - 1 != f[i] {
                // 只有当 i 是硬币时，f[i] 才会因新增面值 i 而 +1
                return Vec::new();
            }
            // i 是新硬币，更新完全背包
            ans.push(i as i32);
            for j in i..=n {
                f[j] += f[j - i];
            }
        }
        ans
    }
}
```
