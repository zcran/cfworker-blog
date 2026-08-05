---
title: "leetcode-计数48"
date: 2026-08-01T10:40:53+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 差的绝对值为 K 的数对数目

给你一个整数数组 nums 和一个整数 k ，请你返回数对 (i, j) 的数目，满足 i < j 且 |nums[i] - nums[j]| == k 。

|x| 的值定义为：

如果 x >= 0 ，那么值为 x 。
如果 x < 0 ，那么值为 -x 。



```
impl Solution {
    pub fn count_k_difference(nums: Vec<i32>, k: i32) -> i32 {
        // 使用哈希表记录每个数字出现次数
        use std::collections::HashMap;
        let mut count = HashMap::new();

        // 统计频次
        for &num in &nums {
            *count.entry(num).or_insert(0) += 1;
        }

        // 对每个数字，查找 num + k 出现的次数
        let mut ans = 0;
        for (&num, &freq) in &count {
            if let Some(&freq_k) = count.get(&(num + k)) {
                ans += freq * freq_k;
            }
        }

        ans
    }
}
```
