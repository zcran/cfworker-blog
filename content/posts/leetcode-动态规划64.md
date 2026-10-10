---
title: "leetcode-动态规划64"
date: 2026-10-05T20:39:58+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 最后一块石头的重量 II

有一堆石头，用整数数组 stones 表示。其中 stones[i] 表示第 i 块石头的重量。

每一回合，从中选出任意两块石头，然后将它们一起粉碎。假设石头的重量分别为 x 和 y，且 x <= y。那么粉碎的可能结果如下：

如果 x == y，那么两块石头都会被完全粉碎；
如果 x != y，那么重量为 x 的石头将会完全粉碎，而重量为 y 的石头新重量为 y-x。

最后，最多只会剩下一块 石头。返回此石头 最小的可能重量 。如果没有石头剩下，就返回 0。




```
impl Solution {
    /// 1049. 最后一块石头的重量 II
    ///
    /// 把石头分成两堆，让两堆重量尽可能接近，剩余最小重量 = |sumA - sumB|。
    /// 等价于 0/1 背包：在容量 target = sum/2 内，求能凑出的最大重量 dp[target]。
    /// 答案 = sum - 2 * dp[target]。
    ///
    /// 用一维布尔数组表示可达和，逆序遍历避免重复使用同一块石头。
    pub fn last_stone_weight_ii(stones: Vec<i32>) -> i32 {
        let sum: i32 = stones.iter().sum();
        let target = (sum / 2) as usize;

        // reachable[s]：能否用部分石头凑出重量 s
        let mut reachable = vec![false; target + 1];
        reachable[0] = true;

        for stone in stones {
            let s = stone as usize;
            if s > target {
                continue; // 超过容量，无法放入
            }
            // 逆序更新，保证每块石头只用一次
            for j in (s..=target).rev() {
                if reachable[j - s] {
                    reachable[j] = true;
                }
            }
        }

        // 找出不超过 target 的最大可达重量
        let best = (0..=target).rev().find(|&s| reachable[s]).unwrap_or(0);
        sum - 2 * best as i32
    }
}
```
