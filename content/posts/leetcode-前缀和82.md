---
title: "leetcode-前缀和82"
date: 2026-08-25T10:02:51+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 使数组元素全部相等的最少操作次数

给你一个正整数数组 nums 。

同时给你一个长度为 m 的整数数组 queries 。第 i 个查询中，你需要将 nums 中所有元素变成 queries[i] 。你可以执行以下操作 任意 次：

将数组里一个元素 增大 或者 减小 1 。

请你返回一个长度为 m 的数组 answer ，其中 answer[i]是将 nums 中所有元素变成 queries[i] 的 最少 操作次数。

注意，每次查询后，数组变回最开始的值。


```
impl Solution {
    pub fn min_operations(mut nums: Vec<i32>, queries: Vec<i32>) -> Vec<i64> {
        // 排序 nums 以便使用二分查找
        nums.sort_unstable();
        let n = nums.len();

        // 构建前缀和数组，prefix[i] 表示 nums[0..=i] 的和
        let mut prefix = vec![0i64; n];
        if n > 0 {
            prefix[0] = nums[0] as i64;
            for i in 1..n {
                prefix[i] = prefix[i - 1] + nums[i] as i64;
            }
        }

        // 处理每个查询
        queries
            .iter()
            .map(|&target| {
                let target = target as i64;
                // 找到第一个 > target 的位置
                let pos = nums.partition_point(|&x| (x as i64) <= target);

                // 左侧部分：所有 <= target 的元素
                let left_count = pos;
                let left_sum = if pos > 0 { prefix[pos - 1] } else { 0 };
                let left_cost = target * left_count as i64 - left_sum;

                // 右侧部分：所有 > target 的元素
                let right_count = n - pos;
                let right_sum = if pos < n {
                    prefix[n - 1] - if pos > 0 { prefix[pos - 1] } else { 0 }
                } else {
                    0
                };
                let right_cost = right_sum - target * right_count as i64;

                left_cost + right_cost
            })
            .collect()
    }
}
```
