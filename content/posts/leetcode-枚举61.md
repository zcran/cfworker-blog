---
title: "leetcode-枚举61"
date: 2026-07-09T10:05:02+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 使数组中所有元素相等的最小开销

给你一个整数数组 nums 和两个整数 cost1 和 cost2 。你可以执行以下 任一 操作 任意 次：

从 nums 中选择下标 i 并且将 nums[i] 增加 1 ，开销为 cost1。
选择 nums 中两个 不同 下标 i 和 j ，并且将 nums[i] 和 nums[j] 都 增加 1 ，开销为 cost2 。
你的目标是使数组中所有元素都 相等 ，请你返回需要的 最小开销 之和。

由于答案可能会很大，请你将它对 10^9 + 7 取余 后返回。


```
impl Solution {
    pub fn min_cost_to_equalize_array(nums: Vec<i32>, cost1: i32, cost2: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let n = nums.len() as i64;
        let c1 = cost1 as i64;
        let c2 = cost2 as i64;

        let min_val = *nums.iter().min().unwrap() as i64;
        let max_val = *nums.iter().max().unwrap() as i64;
        let sum: i64 = nums.iter().map(|&x| x as i64).sum();

        // 将所有元素提升到 max_val 所需的总增量
        let base = n * max_val - sum;

        // 若 n<=2 或单点操作更便宜，直接全用单点操作
        if n <= 2 || c1 * 2 <= c2 {
            return (base * c1 % MOD) as i32;
        }

        // 计算将最大值提升到 x 时的最小开销
        let calc = |x: i64| -> i64 {
            let total = base + (x - max_val) * n; // 总增量
            let diff = x - min_val;               // 最大单点所需增量

            // 尽可能多地使用操作2（配对）
            // 最大配对数为 min(总增量的一半, 除最大需求外的增量总和)
            let pairs = (total / 2).min(total - diff);
            pairs * c2 + (total - 2 * pairs) * c1
        };

        // 候选目标值：max_val 附近以及转折点附近
        let mut candidates = vec![max_val, max_val + 1];

        // 转折点：当 total - diff == total/2 时，即 total = 2*diff
        // 解出 x = (n*max_val - 2*min_val - base) / (n - 2)
        let pivot = (n * max_val - 2 * min_val - base + n - 3) / (n - 2);
        if pivot > max_val {
            for offset in -2..=2 {
                let x = pivot + offset;
                if x >= max_val && x >= 0 {
                    candidates.push(x);
                }
            }
        }

        // 求所有候选值的最小开销
        let mut ans = i64::MAX;
        for &x in &candidates {
            if x >= max_val && x >= 0 {
                ans = ans.min(calc(x));
            }
        }

        (ans % MOD) as i32
    }
}
```
