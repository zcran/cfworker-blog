---
title: "leetcode-双指针138"
date: 2026-09-05T09:53:09+08:00
tags: ["leetcode", "双指针"]
draft: false
---


## 采购方案

小力将 N 个零件的报价存于数组 nums。小力预算为 target，假定小力仅购买两个零件，要求购买零件的花费不超过预算，请问他有多少种采购方案。

注意：答案需要以 1e9 + 7 (1000000007) 为底取模，如：计算初始结果为：1000000008，请返回 1


```
impl Solution {
    pub fn purchase_plans(mut nums: Vec<i32>, target: i32) -> i32 {
        const MOD: i64 = 1_000_000_007;

        nums.sort_unstable();

        // 只保留价格 < target 的零件（价格 >= target 的无法与任何正价零件搭配）
        let right = nums.partition_point(|&x| x < target);
        let nums = &nums[..right];

        let mut count = 0i64;
        // 对每个左零件 i，用双指针/二分找最大的 j 使 nums[i] + nums[j] <= target
        for i in 0..nums.len() {
            // 第一个 > target - nums[i] 的位置
            let j = nums.partition_point(|&x| x <= target - nums[i]);
            if j > i + 1 {
                count += (j - i - 1) as i64;
            }
        }

        (count % MOD) as i32
    }
}
```
