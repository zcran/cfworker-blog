---
title: "leetcode-前缀和133"
date: 2026-08-25T10:02:54+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 最大化特殊下标数目的最少增加次数

给你一个长度为 n 的整数数组 nums。

如果 nums[i] > nums[i - 1] 且 nums[i] > nums[i + 1]，则下标 i (0 < i < n - 1) 是 特殊的 。

你可以执行操作，选择 任意 下标 i 并将 nums[i] 增加 1。

你的目标是：

最大化 特殊 下标的数量。
最小化 达到该 最大值 所需的总 操作 数。

返回所需的 最小 总操作数。

```
impl Solution {
    pub fn min_increase(nums: Vec<i32>) -> i64 {
        let n = nums.len();

        // 后缀代价数组：suf[i] 表示从下标 i 开始，每隔 2 个位置（同奇偶性）修改的代价之和
        // suf[i] 只对 i 与 n-1 同奇偶性的下标有效
        let mut suf = vec![0i64; n + 2];
        for i in (1..n - 1).rev().step_by(2) {
            let cost = (nums[i - 1].max(nums[i + 1]) - nums[i] + 1).max(0) as i64;
            suf[i] = suf[i + 2] + cost;
        }

        // 如果 n 是奇数，所有特殊下标必须位于奇数位置，直接返回总代价
        if n % 2 == 1 {
            return suf[1];
        }

        // n 是偶数，需要枚举分割点
        // 方案一：修改所有偶数下标（2,4,6,...,n-2）
        let mut ans = suf[2];

        // pre 表示当前已经修改的奇数下标累积代价
        let mut pre = 0i64;

        // 枚举分割点：修改 [1..=i] 的奇数下标，修改 [i+3..=n-2] 的偶数下标
        // i 从 1 开始，步长 2，直到 n-3
        for i in (1..n - 1).step_by(2) {
            let cost = (nums[i - 1].max(nums[i + 1]) - nums[i] + 1).max(0) as i64;
            pre += cost;
            // i+3 是下一个未处理的偶数下标起始位置
            ans = ans.min(pre + suf[i + 3]);
        }

        ans
    }
}
```
