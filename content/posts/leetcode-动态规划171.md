---
title: "leetcode-动态规划171"
date: 2026-10-05T20:40:06+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 产生至少 K 个峰值的最少操作次数

给你一个长度为 n 的循环整数数组 nums。

如果下标 i 对应的值 严格大于 其相邻元素，则该下标是一个 峰值 ：

如果 i > 0，下标 i 的 前一个 相邻元素是 nums[i - 1]，否则是 nums[n - 1]。
如果 i < n - 1，下标 i 的 后一个 相邻元素是 nums[i + 1]，否则是 nums[0]。

你可以执行以下操作 任意 次数：

选择任意下标 i 并将 nums[i] 增加 1。

返回使数组包含 至少 k 个峰值所需的 最小 操作数。如果不可能，返回 -1。


```
impl Solution {
    /// 非环形版本：在数组 a 中选出恰好 k 个互不相邻的峰值，最小化操作数
    /// 首尾位置不能作为峰值（候选下标为 1..n-2）
    fn solve(a: &[i32], k: usize) -> i64 {
        let n = a.len();
        const INF: i64 = i32::MAX as i64;

        // f[i]：与 C++ 保持一致，全部初始化为 0，原地滚动更新
        let mut f = vec![0i64; n];

        for left in 1..=k {
            let mut f0 = f[left * 2 - 2];
            let mut f1 = f[left * 2 - 1];
            f[left * 2 - 1] = INF;

            // 后面还需要为剩余 (k - left) 个峰值各留 2 个位置
            let end = n - 1 - (k - left) * 2;
            let mut i = left * 2 - 1;
            while i < end {
                let not_choose = f[i];
                // 让 a[i] 成为峰值所需的代价
                let cost = (a[i - 1].max(a[i + 1]) - a[i] + 1).max(0) as i64;
                let choose = f0 + cost;

                f0 = f1;
                f1 = f[i + 1];
                f[i + 1] = not_choose.min(choose);
                i += 1;
            }
        }

        f[n - 1]
    }

    pub fn min_operations(nums: Vec<i32>, k: i32) -> i32 {
        let n = nums.len();
        let k = k as usize;
        if k > n / 2 {
            return -1;
        }

        // 统计当前已有峰值数量
        let cnt = (0..n)
            .filter(|&i| {
                let prev = nums[(i + n - 1) % n];
                let next = nums[(i + 1) % n];
                prev < nums[i] && nums[i] > next
            })
            .count();
        if cnt >= k {
            return 0;
        }

        // 情况 1：nums[0] 是峰值 → nums[n-1] 不能是峰值
        let mut a = Vec::with_capacity(n + 1);
        a.push(nums[n - 1]);
        a.extend_from_slice(&nums);
        let ans1 = Self::solve(&a, k);

        // 情况 2：nums[0] 不是峰值
        let mut b = nums.clone();
        b.push(nums[0]);
        let ans2 = Self::solve(&b, k);

        ans1.min(ans2) as i32
    }
}
```
