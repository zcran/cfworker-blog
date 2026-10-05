---
title: "leetcode-位运算54"
date: 2026-09-24T10:33:37+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 对数组执行操作使平方和最大

给你一个下标从 0 开始的整数数组 nums 和一个 正 整数 k 。

你可以对数组执行以下操作 任意次 ：

选择两个互不相同的下标 i 和 j ，同时 将 nums[i] 更新为 (nums[i] AND nums[j]) 且将 nums[j] 更新为 (nums[i] OR nums[j]) ，OR 表示按位 或 运算，AND 表示按位 与 运算。
你需要从最终的数组里选择 k 个元素，并计算它们的 平方 之和。

请你返回你可以得到的 最大 平方和。

由于答案可能会很大，将答案对 10^9 + 7 取余 后返回。


```
impl Solution {
    pub fn max_sum(nums: Vec<i32>, k: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;
        const BITS: usize = 30;

        // 统计每一位上共有多少个 1。操作本质上是把各元素的二进制位重新分配，
        // 但不改变每一位上 1 的总数。
        let mut cnt = [0i32; BITS];
        for &x in &nums {
            for i in 0..BITS {
                cnt[i] += (x >> i) & 1;
            }
        }

        // 贪心：每次构造一个尽可能大的数（只要该位还有剩余的 1 就取上），
        // 因为平方和随元素增大而增大，优先填满高位。
        let mut ans: i64 = 0;
        for _ in 0..k {
            let mut x: i64 = 0;
            for i in 0..BITS {
                if cnt[i] > 0 {
                    cnt[i] -= 1;
                    x |= 1 << i;
                }
            }
            ans = (ans + x * x) % MOD;
        }

        ans as i32
    }
}
```
