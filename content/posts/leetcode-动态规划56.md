---
title: "leetcode-动态规划56"
date: 2026-10-05T20:39:57+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 最高的广告牌

你正在安装一个广告牌，并希望它高度最大。这块广告牌将有两个钢制支架，两边各一个。每个钢支架的高度必须相等。

你有一堆可以焊接在一起的钢筋 rods。举个例子，如果钢筋的长度为 1、2 和 3，则可以将它们焊接在一起形成长度为 6 的支架。

返回 广告牌的最大可能安装高度 。如果没法安装广告牌，请返回 0 。



```
impl Solution {
    /// 956. 最高的广告牌
    ///
    /// dp[d] 表示两根支架高度差为 d 时，较矮支架的最大高度（也是答案的候选值）。
    /// 初始 dp[0] = 0，其余为 -1（表示不可达）。
    /// 对每根钢筋 x，用 0/1 背包逆序更新：
    ///   - 放到较高支架：新差值 d + x，较矮支架高度不变
    ///   - 放到较矮支架：新差值 |d - x|，较矮支架高度变为 min(原较矮高度 + x, 原较高高度)...
    ///
    /// 更简洁的等价表述：用 dp[d] 表示"两支架高度差为 d 时，两根支架总长度的最大值"。
    /// 初始 dp[0] = 0；对每根 x，逆序更新：
    ///   ndp[d]     = max(ndp[d],     dp[d])          // 不用 x
    ///   ndp[d + x] = max(ndp[d + x], dp[d] + x)      // x 加到较短/任意一侧
    ///   ndp[|d - x|] = max(ndp[|d - x|], dp[d] + x)  // x 加到较长一侧
    /// 最终答案 = dp[0] / 2（差值 0 即两支架等高，总长一半）。
    ///
    /// 优化：dp[d] 直接存"较矮支架高度"更省心——但为了避免歧义，这里用总长表示，
    /// 最终除以 2，逻辑等价于原 C++ 版本。
    pub fn tallest_billboard(rods: Vec<i32>) -> i32 {
        let sum: usize = rods.iter().map(|&x| x as usize).sum();
        const NEG: i32 = i32::MIN / 2; // 表示不可达，且加法不会溢出
        let mut dp = vec![NEG; sum + 1];
        dp[0] = 0;

        for &x in &rods {
            let x = x as usize;
            let mut next = dp.clone();
            for d in 0..=sum {
                if dp[d] < 0 {
                    continue;
                }
                // 放到一侧：差变为 d + x
                if d + x <= sum {
                    next[d + x] = next[d + x].max(dp[d] + x as i32);
                }
                // 放到另一侧：差变为 |d - x|
                let diff = (d as i32 - x as i32).unsigned_abs() as usize;
                next[diff] = next[diff].max(dp[d] + x as i32);
            }
            dp = next;
        }

        dp[0] / 2
    }
}
```
