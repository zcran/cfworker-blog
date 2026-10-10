---
title: "leetcode-数组34"
date: 2026-10-10T20:38:42+08:00
tags: ["leetcode", "数组"]
draft: false
---


## 统计数组中相等且可以被整除的数对

给你一个下标从 0 开始长度为 n 的整数数组 nums 和一个整数 k ，请你返回满足 0 <= i < j < n ，nums[i] == nums[j] 且 (i * j) 能被 k 整除的数对 (i, j) 的 数目 。




```
impl Solution {
    /// 统计满足 `i < j`、`nums[i] == nums[j]` 且 `(i * j) % k == 0` 的数对数目。
    ///
    /// 枚举所有下标对 `(i, j)`，检查值相等且下标乘积能被 `k` 整除。
    /// 数据规模较小（n ≤ 100），O(n²) 枚举足够。
    ///
    /// 时间复杂度 O(n²)，空间复杂度 O(1)。
    pub fn count_pairs(nums: Vec<i32>, k: i32) -> i32 {
        let n = nums.len();
        let k = k as usize;
        let mut count = 0;

        for i in 0..n {
            for j in i + 1..n {
                if nums[i] == nums[j] && (i * j) % k == 0 {
                    count += 1;
                }
            }
        }

        count
    }
}
```
