---
title: "leetcode-二分查找49"
date: 2026-09-28T12:29:13+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 出租车的最大盈利

你驾驶出租车行驶在一条有 n 个地点的路上。这 n 个地点从近到远编号为 1 到 n ，你想要从 1 开到 n ，通过接乘客订单盈利。你只能沿着编号递增的方向前进，不能改变方向。

乘客信息用一个下标从 0 开始的二维数组 rides 表示，其中 rides[i] = [starti, endi, tipi] 表示第 i 位乘客需要从地点 starti 前往 endi ，愿意支付 tipi 元的小费。

每一位 你选择接单的乘客 i ，你可以 盈利 endi - starti + tipi 元。你同时 最多 只能接一个订单。

给你 n 和 rides ，请你返回在最优接单方案下，你能盈利 最多 多少元。

注意：你可以在一个地点放下一位乘客，并在同一个地点接上另一位乘客。


```
impl Solution {
    pub fn max_taxi_earnings(n: i32, rides: Vec<Vec<i32>>) -> i64 {
        let n = n as usize;
        // groups[end] 存储所有在该点结束的订单 (start, 盈利)
        let mut groups: Vec<Vec<(usize, i64)>> = vec![Vec::new(); n + 1];
        for r in &rides {
            let (start, end, tip) = (r[0] as usize, r[1] as usize, r[2] as i64);
            groups[end].push((start, (end - start) as i64 + tip));
        }

        // dp[i]：到达地点 i 时能获得的最大盈利
        let mut dp = vec![0i64; n + 1];
        for i in 1..=n {
            // 不接任何在此结束的订单，直接从 i-1 前进
            dp[i] = dp[i - 1];
            // 尝试接所有在此结束的订单
            for &(start, gain) in &groups[i] {
                dp[i] = dp[i].max(dp[start] + gain);
            }
        }

        dp[n]
    }
}
```
