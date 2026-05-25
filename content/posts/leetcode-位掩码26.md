---
title: "leetcode-位掩码26"
date: 2026-05-18T10:27:59+08:00
tags: ["leetcode", "位掩码"]
draft: false
---


## 公平分发饼干

给你一个整数数组 cookies ，其中 cookies[i] 表示在第 i 个零食包中的饼干数量。另给你一个整数 k 表示等待分发零食包的孩子数量，所有 零食包都需要分发。在同一个零食包中的所有饼干都必须分发给同一个孩子，不能分开。

分发的 不公平程度 定义为单个孩子在分发过程中能够获得饼干的最大总数。

返回所有分发的最小不公平程度。



```
impl Solution {
    /// 将 cookies 全部分给 k 个孩子，最小化得到最多饼干的孩子手中的饼干总数
    pub fn distribute_cookies(cookies: Vec<i32>, k: i32) -> i32 {
        let k = k as usize;
        let n = cookies.len();
        let total = 1 << n;                      // 所有子集数量

        // 1. 预计算每个子集的饼干总和（利用 lowbit 递推）
        let mut sum = vec![0; total];
        for mask in 1..total {
            let lowbit = mask & (mask as i32).wrapping_neg() as usize;
            let idx = lowbit.trailing_zeros() as usize;
            sum[mask] = sum[mask ^ lowbit] + cookies[idx];
        }

        // 2. 滚动 DP：dp_prev[mask] = 处理完前 i 个孩子后，已分配饼干集合为 mask 的最小不公平度
        let mut dp_prev = vec![i32::MAX; total];
        dp_prev[0] = 0;                          // 0 个孩子，空集不公平度为 0

        for _ in 0..k {                          // 依次考虑每个孩子
            let mut dp_cur = vec![i32::MAX; total];
            dp_cur[0] = 0;                       // 当前孩子不分饼干，不公平度继承

            for mask in 1..total {
                // 基础情况：当前孩子分得空集，不公平度 = 前 i-1 个孩子的结果
                let mut best = dp_prev[mask];
                let mut sub = mask;
                // 枚举当前孩子能分到的所有非空子集 sub（sub ⊆ mask）
                while sub != 0 {
                    let unfair = dp_prev[mask ^ sub].max(sum[sub]);
                    if unfair < best { best = unfair; }
                    sub = (sub - 1) & mask;      // 下一个真子集
                }
                dp_cur[mask] = best;
            }
            dp_prev = dp_cur;
        }

        dp_prev[total - 1]                       // 所有饼干分完的最优值
    }
}
```
