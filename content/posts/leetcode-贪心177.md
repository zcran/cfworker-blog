---
title: "leetcode-贪心177"
date: 2026-10-03T12:55:46+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## K 个元素的最大总和

给你一个整数数组 nums，以及两个整数 k 和 mul。

从 nums 中选出 恰好 k 个元素。你可以按照任意顺序逐个处理这些元素。

对于每个被选择的元素，都可以 独立地 选择以下两种操作之一：

将该元素的值 加 到总和中；或

将该元素乘以 mul 的 当前 值，并将结果 加 到总和中。

每处理一个被选择的元素后，无论选择哪种操作，mul 都会 减少 1。mul 的当前值可能变为 0 或负数。

返回一个整数，表示可能得到的 最大 总和。


```
impl Solution {
    pub fn max_sum(mut nums: Vec<i32>, k: i32, mul: i32) -> i64 {
        // 贪心：系数 mul 递减，越靠前的选择系数越大，
        // 因此把最大的 k 个元素排在前面，依次乘以 max(mul, 1)。
        // 当 mul 降到 <= 1 时，乘法不再有增益，用 1 即可（等价于直接加）。
        nums.sort_unstable_by(|a, b| b.cmp(a));

        let mut ans = 0i64;
        for i in 0..k as usize {
            let coeff = (mul - i as i32).max(1) as i64;
            ans += nums[i] as i64 * coeff;
        }
        ans
    }
}
```
