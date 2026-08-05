---
title: "leetcode-滑动窗口17"
date: 2026-07-18T11:02:30+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 新 21 点

爱丽丝参与一个大致基于纸牌游戏 “21点” 规则的游戏，描述如下：

爱丽丝以 0 分开始，并在她的得分少于 k 分时抽取数字。 抽取时，她从 [1, maxPts] 的范围中随机获得一个整数作为分数进行累计，其中 maxPts 是一个整数。 每次抽取都是独立的，其结果具有相同的概率。

当爱丽丝获得 k 分 或更多分 时，她就停止抽取数字。

爱丽丝的分数不超过 n 的概率是多少？

与实际答案误差不超过 10^-5 的答案将被视为正确答案。




```
impl Solution {
    pub fn new21_game(n: i32, k: i32, max_pts: i32) -> f64 {
        let n = n as usize;
        let k = k as usize;
        let max_pts = max_pts as usize;

        // dp[i]：从分数 i 开始，最终获胜（分数不超过 n）的概率
        let mut dp = vec![0.0; n + max_pts + 1];
        let mut window_sum = 0.0; // 维护 dp[i+1] + ... + dp[i+max_pts]

        // 从后向前计算，i 从 n 递减到 0
        for i in (0..=n).rev() {
            if i >= k {
                // 已经达到停止条件，获胜概率为 1
                dp[i] = 1.0;
            } else {
                // 否则，下一步抽到 1..max_pts，概率为这些状态的平均值
                dp[i] = window_sum / max_pts as f64;
            }

            // 更新窗口和，为下一轮 i-1 准备
            // 窗口需要包含 dp[i]（因为下一轮会用到 dp[i]）
            window_sum += dp[i];
            // 如果 i+max_pts 在数组范围内，移除超出窗口的部分
            if i + max_pts <= n + max_pts {
                window_sum -= dp[i + max_pts];
            }
        }

        dp[0]
    }
}
```
