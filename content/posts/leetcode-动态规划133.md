---
title: "leetcode-动态规划133"
date: 2026-10-05T20:40:03+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 最大化子数组的总成本

给你一个长度为 n 的整数数组 nums。

子数组 nums[l..r]（其中 0 <= l <= r < n）的 成本 定义为：

cost(l, r) = nums[l] - nums[l + 1] + ... + nums[r] * (−1)^r − l

你的任务是将 nums 分割成若干子数组，使得所有子数组的成本之和 最大化，并确保每个元素 正好 属于一个子数组。

具体来说，如果 nums 被分割成 k 个子数组，且分割点为索引 i1, i2, ..., iˇ(k − 1)（其中 0 <= iˇ1 < iˇ2 < ... < iˇ(k - 1) < n - 1），则总成本为：

cost(0, iˇ1) + cost(iˇ1 + 1, iˇ2) + ... + cost(iˇ(k − 1) + 1, n − 1)

返回在最优分割方式下的子数组成本之和的最大值。

注意：如果 nums 没有被分割，即 k = 1，则总成本即为 cost(0, n - 1)。




```
impl Solution {
    pub fn maximum_total_cost(nums: Vec<i32>) -> i64 {
        // f0: 处理到前一个元素时，最后一个子数组长度为偶数的最大总成本
        // f1: 处理到前一个元素时，最后一个子数组长度为奇数的最大总成本
        let mut f0 = 0i64;
        let mut f1 = nums[0] as i64;

        for i in 1..nums.len() {
            let x = nums[i] as i64;
            let prev = nums[i - 1] as i64;
            let tmp = f1;
            // 要么把 nums[i] 接到当前子数组末尾（延续奇偶性翻转），
            // 要么把 nums[i-1] 从原位置拆出、让 nums[i] 开始新的偶数长度段
            f1 = (f1 + x).max(f0 + prev - x);
            f0 = tmp;
        }

        f1
    }
}
```
