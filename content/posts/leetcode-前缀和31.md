---
title: "leetcode-前缀和31"
date: 2026-08-25T10:02:47+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 所有排列中的最大和

有一个整数数组 nums ，和一个查询数组 requests ，其中 requests[i] = [starti, endi] 。第 i 个查询求 nums[starti] + nums[starti + 1] + ... + nums[endi - 1] + nums[endi] 的结果 ，starti 和 endi 数组索引都是 从 0 开始 的。

你可以任意排列 nums 中的数字，请你返回所有查询结果之和的最大值。

由于答案可能会很大，请你将它对 10^9 + 7 取余 后返回。


```
impl Solution {
    /// 计算所有查询结果之和的最大值
    ///
    /// 思路：
    /// 1. 统计每个位置被查询的频率（使用差分数组）
    /// 2. 频率高的位置分配大的数字（贪心）
    /// 3. 返回最大和
    pub fn max_sum_range_query(mut nums: Vec<i32>, requests: Vec<Vec<i32>>) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let n = nums.len();

        // 差分数组统计每个位置被覆盖的次数
        let mut diff = vec![0i64; n + 1];
        for req in requests {
            let l = req[0] as usize;
            let r = req[1] as usize;
            diff[l] += 1;
            diff[r + 1] -= 1;
        }

        // 计算每个位置的实际覆盖次数
        let mut freq = Vec::with_capacity(n);
        let mut cur = 0i64;
        for i in 0..n {
            cur += diff[i];
            freq.push(cur);
        }

        // 排序：频率升序，数字升序
        freq.sort_unstable();
        nums.sort_unstable();

        // 最大和 = 最大频率 × 最大数字
        let mut ans = 0i64;
        for (count, &num) in freq.iter().zip(nums.iter()).rev() {
            ans = (ans + count * num as i64) % MOD;
        }

        ans as i32
    }
}
```
