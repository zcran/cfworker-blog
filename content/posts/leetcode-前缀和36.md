---
title: "leetcode-前缀和36"
date: 2026-08-25T10:02:48+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 将数组分成三个子数组的方案数

我们称一个分割整数数组的方案是 好的 ，当它满足：

数组被分成三个 非空 连续子数组，从左至右分别命名为 left ， mid ， right 。

left 中元素和小于等于 mid 中元素和，mid 中元素和小于等于 right 中元素和。

给你一个 非负 整数数组 nums ，请你返回 好的 分割 nums 方案数目。由于答案可能会很大，请你将结果对 10^9 + 7 取余后返回。


```
impl Solution {
    /// 计算将数组分割成三个非空连续子数组且满足 left_sum <= mid_sum <= right_sum 的方案数
    ///
    /// 使用前缀和，对于每个可能的右边界 r（mid 的右端），二分查找可行的左边界 l 的范围
    pub fn ways_to_split(nums: Vec<i32>) -> i32 {
        const MOD: i64 = 1_000_000_007;
        let n = nums.len();

        // 前缀和，pref[i] 表示前 i 个元素之和
        let mut pref = vec![0i64; n + 1];
        for (i, &x) in nums.iter().enumerate() {
            pref[i + 1] = pref[i] + x as i64;
        }
        let total = pref[n];

        let mut ans = 0i64;

        // 枚举 mid 的右边界 r，mid 至少有一个元素，所以 r >= 2，且 r <= n-1
        for r in 2..n {
            // 对于固定的 r，l 需满足：
            // left_sum = pref[l], mid_sum = pref[r] - pref[l], right_sum = total - pref[r]
            // 条件：pref[l] <= pref[r] - pref[l]  =>  2*pref[l] <= pref[r]  =>  pref[l] <= pref[r]/2
            // 且 pref[r] - pref[l] <= total - pref[r]  =>  pref[l] >= 2*pref[r] - total
            let lower = 2 * pref[r] - total;
            let upper = pref[r] / 2;

            // 在 l 的可能范围 [1, r-1] 中寻找满足 lower <= pref[l] <= upper 的个数
            // 使用 partition_point 在 slice pref[1..r] 上二分
            let slice = &pref[1..r]; // 包含索引 1 到 r-1
            let left_idx = slice.partition_point(|&x| x < lower);  // 第一个 >= lower 的位置
            let right_idx = slice.partition_point(|&x| x <= upper); // 第一个 > upper 的位置
            if left_idx < right_idx {
                ans = (ans + (right_idx - left_idx) as i64) % MOD;
            }
        }

        ans as i32
    }
}
```
