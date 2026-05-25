---
title: "leetcode-树状树组22"
date: 2026-05-03T20:07:53+08:00
tags: ["leetcode", "树状树组"]
draft: false
---


## 最大和查询

给你两个长度为 n 、下标从 0 开始的整数数组 nums1 和 nums2 ，另给你一个下标从 1 开始的二维数组 queries ，其中 queries[i] = [xi, yi] 。

对于第 i 个查询，在所有满足 nums1[j] >= xi 且 nums2[j] >= yi 的下标 j (0 <= j < n) 中，找出 nums1[j] + nums2[j] 的 最大值 ，如果不存在满足条件的 j 则返回 -1 。

返回数组 answer ，其中 answer[i] 是第 i 个查询的答案。

```
use std::cmp::Reverse;

impl Solution {
    /// 计算所有查询的最大可能和。
    /// 给定数组 nums1 和 nums2，对于每个查询 (x, y)，
    /// 需要找到满足 nums1[i] >= x 且 nums2[i] >= y 的下标 i，
    /// 返回这些下标中 nums1[i] + nums2[i] 的最大值，若不存在则返回 -1。
    ///
    /// 算法：
    /// 1. 将 (nums1[i], nums2[i]) 配对并按 nums1 降序排序。
    /// 2. 将查询带上原始索引，并按 x 降序排序。
    /// 3. 维护单调栈，栈中元素为 (nums2, sum) 且满足：
    ///    - nums2 严格递增（以保证二分查找有效）
    ///    - sum 严格递减（保证栈顶 sum 是当前范围的最小值，便于弹出无用项）
    /// 4. 按 x 降序遍历查询，同时将所有 nums1 >= x 的配对加入栈中：
    ///    - 若新元素的 nums2 <= 栈顶的 nums2，则跳过（不会改善结果）
    ///    - 否则，弹出栈中所有 sum <= 新元素 sum 的项（因为它们被覆盖），然后压入新元素。
    /// 5. 对于查询 (x, y)，在栈中二分查找第一个 nums2 >= y 的位置，取对应的 sum 作为答案。
    pub fn maximum_sum_queries(
        nums1: Vec<i32>,
        nums2: Vec<i32>,
        queries: Vec<Vec<i32>>,
    ) -> Vec<i32> {
        // 1. 配对并按 nums1 降序排序
        let mut pairs: Vec<(i32, i32)> = nums1.into_iter().zip(nums2).collect();
        pairs.sort_unstable_by_key(|&(x, _)| Reverse(x));

        // 2. 查询带上索引并按 x 降序排序
        let mut indexed_queries: Vec<(usize, i32, i32)> = queries
            .into_iter()
            .enumerate()
            .map(|(i, q)| (i, q[0], q[1]))
            .collect();
        indexed_queries.sort_unstable_by_key(|&(_, x, _)| Reverse(x));

        let mut stack: Vec<(i32, i32)> = Vec::new();  // (nums2, sum)
        let mut results = vec![-1; indexed_queries.len()];
        let mut pair_idx = 0;  // 手动控制 pairs 的遍历索引

        // 3. 处理每个查询
        for (orig_idx, x, y) in indexed_queries {
            // 将所有满足 nums1 >= x 的配对加入栈中
            while pair_idx < pairs.len() && pairs[pair_idx].0 >= x {
                let (a, b) = pairs[pair_idx];
                let sum = a + b;

                // 只有当 b > 栈顶的 nums2 时，才可能产生新的更优值
                if let Some(&(last_b, _)) = stack.last() {
                    if b <= last_b {
                        // 当前 b 不大于栈顶，直接跳过（不会对后续查询有贡献）
                        pair_idx += 1;
                        continue;
                    }
                }

                // 弹出所有 sum 小于等于当前 sum 的栈顶元素（保留 sum 更大的）
                while let Some(&(_, last_sum)) = stack.last() {
                    if sum > last_sum {
                        stack.pop();
                    } else {
                        break;
                    }
                }
                stack.push((b, sum));
                pair_idx += 1;
            }

            // 在栈中二分查找第一个 nums2 >= y 的位置
            let pos = stack.partition_point(|&(b, _)| b < y);
            if pos < stack.len() {
                results[orig_idx] = stack[pos].1;
            }
        }

        results
    }
}
```
