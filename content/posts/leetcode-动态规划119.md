---
title: "leetcode-动态规划119"
date: 2026-10-05T20:40:02+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 访问数组中的位置使分数最大

给你一个下标从 0 开始的整数数组 nums 和一个正整数 x 。

你 一开始 在数组的位置 0 处，你可以按照下述规则访问数组中的其他位置：

如果你当前在位置 i ，那么你可以移动到满足 i < j 的 任意 位置 j 。
对于你访问的位置 i ，你可以获得分数 nums[i] 。
如果你从位置 i 移动到位置 j 且 nums[i] 和 nums[j] 的 奇偶性 不同，那么你将失去分数 x 。

请你返回你能得到的 最大 得分之和。

注意 ，你一开始的分数为 nums[0] 。




```
impl Solution {
    pub fn max_score(nums: Vec<i32>, x: i32) -> i64 {
        // dp[p]: 以奇偶性 p 结尾（即最后访问位置奇偶性为 p）时的最大得分
        // 由于 nums[i] 可能为负，用 i64::MIN/2 表示不可达
        const NEG: i64 = i64::MIN / 2;
        let mut dp = [NEG, NEG];
        dp[(nums[0] & 1) as usize] = nums[0] as i64;

        let mut ans = nums[0] as i64;
        for &v in &nums[1..] {
            let p = (v & 1) as usize;
            // 可以从同奇偶的位置直接过来，或从异奇偶位置跳过来并扣 x
            let cur = (dp[p] + v as i64).max(dp[1 - p] - x as i64 + v as i64);
            ans = ans.max(cur);
            dp[p] = dp[p].max(cur);
        }

        ans
    }
}
```
