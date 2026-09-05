---
title: "leetcode-前缀和123"
date: 2026-08-25T10:02:53+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 边界与内部和相等的稳定子数组

给你一个整数数组 capacity。

当满足以下条件时，子数组 capacity[l..r] 被视为 稳定 数组：

其长度 至少 为 3。

首 元素与 尾 元素都等于它们之间所有元素的 和（即 capacity[l] = capacity[r] = capacity[l + 1] + capacity[l + 2] + ... + capacity[r - 1]）。

返回一个整数，表示 稳定子数组 的数量。

子数组 是数组中的连续且非空的元素序列。


```
use std::collections::HashMap;

impl Solution {
    pub fn count_stable_subarrays(capacity: Vec<i32>) -> i64 {
        let n = capacity.len();
        if n < 3 {
            return 0;
        }

        // key: (左端点值, 左端点前缀和)，value: 出现次数
        let mut map = HashMap::new();
        let mut prefix_sum = capacity[0] as i64;
        let mut ans = 0i64;

        for r in 1..n {
            // 以 capacity[r] 为右端点，查找符合条件的左端点
            // 条件：左端点值 = capacity[r]，且左端点前缀和 = sum(左端点+1..右端点-1)
            // 即 prefix_sum[左端点] = 左端点值 + 中间元素和
            // 而当前前缀和 sum = capacity[r] + 中间元素和 + capacity[左端点]
            // 所以 prefix_sum[左端点] = sum - capacity[r]
            let key = (capacity[r], prefix_sum);
            ans += map.get(&key).copied().unwrap_or(0);

            // 将当前 r-1 作为左端点加入哈希表
            // 左端点值 = capacity[r-1]
            // 左端点前缀和 = 左端点值 + capacity[r-1] + 前面元素和
            // 即 capacity[r-1] + prefix_sum_before_r-1
            let left_val = capacity[r - 1] as i64;
            let left_prefix = left_val + prefix_sum;
            let key = (capacity[r - 1], left_prefix);
            *map.entry(key).or_insert(0) += 1;

            // 更新前缀和，加入当前元素 capacity[r]
            prefix_sum += capacity[r] as i64;
        }

        ans
    }
}
```
