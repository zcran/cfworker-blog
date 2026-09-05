---
title: "leetcode-前缀和74"
date: 2026-08-25T10:02:50+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 商店的最少代价

给你一个顾客访问商店的日志，用一个下标从 0 开始且只包含字符 'N' 和 'Y' 的字符串 customers 表示：

如果第 i 个字符是 'Y' ，它表示第 i 小时有顾客到达。
如果第 i 个字符是 'N' ，它表示第 i 小时没有顾客到达。

如果商店在第 j 小时关门（0 <= j <= n），代价按如下方式计算：

在开门期间，如果某一个小时没有顾客到达，代价增加 1 。
在关门期间，如果某一个小时有顾客到达，代价增加 1 。

请你返回在确保代价 最小 的前提下，商店的 最早 关门时间。

注意，商店在第 j 小时关门表示在第 j 小时以及之后商店处于关门状态。


```
impl Solution {
    pub fn best_closing_time(customers: String) -> i32 {
        let s = customers.as_bytes();
        let n = s.len();

        // 先计算所有'Y'的数量，作为初始后缀代价（关门期间遇到'Y'的惩罚）
        let total_y = s.iter().filter(|&&c| c == b'Y').count() as i32;

        let mut min_cost = total_y;  // 在0时刻关门的初始代价
        let mut best_time = 0;
        let mut cost = total_y;      // 当前关门的代价

        // 依次尝试在1..=n时刻关门
        for i in 1..=n {
            // 根据第 i-1 小时的情况更新代价
            // 如果该小时是 'Y'：从开门变为关门，原本要惩罚现在不需要了
            // 如果该小时是 'N'：从开门变为关门，原本不需要惩罚现在要惩罚
            if s[i - 1] == b'Y' {
                cost -= 1;  // 关门期间少了一个'Y'的惩罚
            } else {
                cost += 1;  // 开门期间多了一个'N'的惩罚
            }

            // 更新最小代价和最早关门时间
            if cost < min_cost {
                min_cost = cost;
                best_time = i as i32;
            }
        }

        best_time
    }
}
```
