---
title: "leetcode-计数37"
date: 2026-08-01T10:40:52+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 盒子中小球的最大数量

你在一家生产小球的玩具厂工作，有 n 个小球，编号从 lowLimit 开始，到 highLimit 结束（包括 lowLimit 和 highLimit ，即 n == highLimit - lowLimit + 1）。另有无限数量的盒子，编号从 1 到 infinity 。

你的工作是将每个小球放入盒子中，其中盒子的编号应当等于小球编号上每位数字的和。例如，编号 321 的小球应当放入编号 3 + 2 + 1 = 6 的盒子，而编号 10 的小球应当放入编号 1 + 0 = 1 的盒子。

给你两个整数 lowLimit 和 highLimit ，返回放有最多小球的盒子中的小球数量。如果有多个盒子都满足放有最多小球，只需返回其中任一盒子的小球数量。


```
impl Solution {
    pub fn count_balls(low_limit: i32, high_limit: i32) -> i32 {
        // 数字和最大为 9*5=45（因为 high_limit <= 10^5），稍留余量
        let mut count = vec![0; 50];

        for mut num in low_limit..=high_limit {
            let mut sum = 0;
            // 计算每个数字的数位和（避免字符串转换）
            while num > 0 {
                sum += num % 10;
                num /= 10;
            }
            count[sum as usize] += 1;
        }

        *count.iter().max().unwrap()
    }
}
```
