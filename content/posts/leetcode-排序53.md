---
title: "leetcode-排序53"
date: 2026-10-05T00:35:03+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 最小移动总距离

X 轴上有一些机器人和工厂。给你一个整数数组 robot ，其中 robot[i] 是第 i 个机器人的位置。再给你一个二维整数数组 factory ，其中 factory[j] = [positionj, limitj] ，表示第 j 个工厂的位置在 positionj ，且第 j 个工厂最多可以修理 limitj 个机器人。

每个机器人所在的位置 互不相同 。每个工厂所在的位置也 互不相同 。注意一个机器人可能一开始跟一个工厂在 相同的位置 。

所有机器人一开始都是坏的，他们会沿着设定的方向一直移动。设定的方向要么是 X 轴的正方向，要么是 X 轴的负方向。当一个机器人经过一个没达到上限的工厂时，这个工厂会维修这个机器人，且机器人停止移动。

任何时刻，你都可以设置 部分 机器人的移动方向。你的目标是最小化所有机器人总的移动距离。

请你返回所有机器人移动的最小总距离。测试数据保证所有机器人都可以被维修。

注意：

所有机器人移动速度相同。
如果两个机器人移动方向相同，它们永远不会碰撞。
如果两个机器人迎面相遇，它们也不会碰撞，它们彼此之间会擦肩而过。
如果一个机器人经过了一个已经达到上限的工厂，机器人会当作工厂不存在，继续移动。
机器人从位置 x 到位置 y 的移动距离为 |y - x| 。



```
impl Solution {
    /// 返回所有机器人移动的最小总距离。
    ///
    /// 思路（动态规划）：
    /// - 将机器人和工厂都按位置升序排序。
    /// - dp[i][j] 表示用前 j 个工厂修理前 i 个机器人（按位置排序后）的最小总距离。
    /// - 转移：第 j 个工厂可以修理最后 k 个机器人（1 <= k <= limit_j），
    ///   此时这 k 个机器人依次匹配到该工厂，总距离为
    ///   dp[i-k][j-1] + sum |robot[i-t] - factory[j-1].position|。
    /// - dp[0][j] = 0，其余初始为足够大的值。
    pub fn minimum_total_distance(mut robot: Vec<i32>, mut factory: Vec<Vec<i32>>) -> i64 {
        robot.sort_unstable();
        factory.sort_unstable();

        let n = robot.len();
        let m = factory.len();
        const INF: i64 = i64::MAX / 2;

        // dp[i][j]：前 i 个机器人由前 j 个工厂修理的最小总距离
        let mut dp = vec![vec![INF; m + 1]; n + 1];
        for j in 0..=m {
            dp[0][j] = 0;
        }

        for j in 1..=m {
            let (pos, limit) = (factory[j - 1][0] as i64, factory[j - 1][1] as usize);
            for i in 1..=n {
                // 不使用第 j 个工厂
                dp[i][j] = dp[i][j - 1];

                // 使用第 j 个工厂修理最后 k 个机器人
                let mut cost = 0i64;
                let max_k = i.min(limit);
                for k in 1..=max_k {
                    cost += (robot[i - k] as i64 - pos).abs();
                    dp[i][j] = dp[i][j].min(dp[i - k][j - 1] + cost);
                }
            }
        }

        dp[n][m]
    }
}
```
