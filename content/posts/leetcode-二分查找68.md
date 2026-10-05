---
title: "leetcode-二分查找68"
date: 2026-09-28T12:29:14+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 销售利润最大化

给你一个整数 n 表示数轴上的房屋数量，编号从 0 到 n - 1 。

另给你一个二维整数数组 offers ，其中 offers[i] = [starti, endi, goldi] 表示第 i 个买家想要以 goldi 枚金币的价格购买从 starti 到 endi 的所有房屋。

作为一名销售，你需要有策略地选择并销售房屋使自己的收入最大化。

返回你可以赚取的金币的最大数目。

注意 同一所房屋不能卖给不同的买家，并且允许保留一些房屋不进行出售。




```
impl Solution {
    pub fn maximize_the_profit(n: i32, mut offers: Vec<Vec<i32>>) -> i32 {
        // 按结束位置排序，便于二分查找不重叠的前一个 offer
        offers.sort_unstable_by_key(|o| o[1]);

        let m = offers.len();
        // dp[i] 表示前 i 个 offer 中能获得的最大金币数（i 从 1 开始）
        let mut dp = vec![0_i32; m + 1];

        for i in 1..=m {
            let (start, _, gold) = (offers[i - 1][0], offers[i - 1][1], offers[i - 1][2]);

            // 找到最后一个 end < start 的 offer 下标（0-based），其 dp 下标为 idx + 1
            let idx = offers[..i - 1].partition_point(|o| o[1] < start);
            let buy = gold + dp[idx];

            // 不选当前 offer
            let not_buy = dp[i - 1];

            dp[i] = buy.max(not_buy);
        }

        dp[m]
    }
}
```
