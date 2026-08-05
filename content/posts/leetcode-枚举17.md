---
title: "leetcode-枚举17"
date: 2026-07-09T10:05:00+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 统计特殊四元组

给你一个 下标从 0 开始 的整数数组 nums ，返回满足下述条件的 不同 四元组 (a, b, c, d) 的 数目 ：

nums[a] + nums[b] + nums[c] == nums[d] ，且

a < b < c < d


```
impl Solution {
    /// 统计满足 nums[a] + nums[b] + nums[c] == nums[d] 且 a < b < c < d 的四元组数量。
    ///
    /// 算法：枚举 c 和 d，用哈希表存储所有 a < b < c 的两数之和
    /// 时间复杂度 O(n²)，空间复杂度 O(n²)
    pub fn count_quadruplets(nums: Vec<i32>) -> i32 {
        use std::collections::HashMap;

        let n = nums.len();
        let mut count = 0;
        // 存储两数之和 nums[a] + nums[b] 的出现次数，其中 a < b < 当前 c
        let mut sum_count: HashMap<i32, i32> = HashMap::new();

        // 枚举 c（第三个元素），c 从 2 开始，最多到 n-2（因为 d > c）
        for c in 2..n - 1 {
            // 将 nums[c-1] 与所有前面的数 nums[a] 组合，添加到哈希表
            // 这样哈希表中就包含了所有 a < b <= c-1 的两数之和
            // 对于当前 c，我们需要 a < b < c，所以 b 最大为 c-1
            for a in 0..c - 1 {
                *sum_count.entry(nums[a] + nums[c - 1]).or_insert(0) += 1;
            }

            // 枚举 d > c，统计满足 nums[a] + nums[b] == nums[d] - nums[c] 的四元组
            for d in c + 1..n {
                let target = nums[d] - nums[c];
                if let Some(&cnt) = sum_count.get(&target) {
                    count += cnt;
                }
            }
        }

        count
    }
}
```
