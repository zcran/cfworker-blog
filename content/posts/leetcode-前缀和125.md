---
title: "leetcode-前缀和125"
date: 2026-08-25T10:02:53+08:00
tags: ["leetcode", "前缀和"]
draft: false
---


## 统计稳定子数组的数目

给你一个整数数组 nums。

如果 nums 的一个 子数组 中 没有逆序对 ，即不存在满足 i < j 且 nums[i] > nums[j] 的下标对，则该子数组被称为 稳定 子数组。

同时给你一个长度为 q 的 二维整数数组 queries，其中每个 queries[i] = [li, ri] 表示一个查询。对于每个查询 [li, ri]，请你计算完全包含在 nums[li..ri] 内的 稳定子数组 的数量。

返回一个长度为 q 的整数数组 ans，其中 ans[i] 是第 i 个查询的答案。

注意：

子数组 是数组中一个连续且 非空 的元素序列。

单个元素的子数组被认为是稳定的。


```
impl Solution {
    pub fn count_stable_subarrays(nums: Vec<i32>, queries: Vec<Vec<i32>>) -> Vec<i64> {
        let n = nums.len();

        // 1. 找到所有严格递增段的起始位置
        let mut starts = Vec::new();    // 每个递增段的左端点
        let mut pref = vec![0i64];      // 递增子数组数量的前缀和
        let mut start = 0;

        for i in 0..n {
            // 当 i 是段尾时（最后一个元素 或 当前元素 > 下一个元素）
            if i == n - 1 || nums[i] > nums[i + 1] {
                starts.push(start);
                let len = (i - start + 1) as i64;
                // len 长度的数组有 len*(len+1)/2 个稳定子数组（所有子数组都稳定）
                pref.push(pref.last().unwrap() + len * (len + 1) / 2);
                start = i + 1;
            }
        }

        // 2. 处理每个查询
        let mut ans = Vec::with_capacity(queries.len());
        for q in &queries {
            let l = q[0] as usize;
            let r = q[1] as usize;

            // 找到第一个起始位置 > l 的段索引
            let i = starts.partition_point(|&x| x <= l);
            // 找到最后一个起始位置 <= r 的段索引（即包含 r 的段）
            let j = starts.partition_point(|&x| x <= r) - 1;

            if i > j {
                // l 和 r 在同一个递增段内
                let len = (r - l + 1) as i64;
                ans.push(len * (len + 1) / 2);
            } else {
                // l 和 r 跨越多个递增段
                // 计算三部分：
                // 1) [l, starts[i]) 部分长度
                let left_len = (starts[i] - l) as i64;
                // 2) 中间完整段 [starts[i], starts[j])
                let mid = pref[j] - pref[i];
                // 3) [starts[j], r] 部分长度
                let right_len = (r - starts[j] + 1) as i64;

                let total = left_len * (left_len + 1) / 2
                          + mid
                          + right_len * (right_len + 1) / 2;
                ans.push(total);
            }
        }

        ans
    }
}
```
