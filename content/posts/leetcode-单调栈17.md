---
title: "leetcode-单调栈17"
date: 2026-06-07T10:14:04+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 最大宽度坡

给定一个整数数组 A，坡是元组 (i, j)，其中  i < j 且 A[i] <= A[j]。这样的坡的宽度为 j - i。

找出 A 中的坡的最大宽度，如果不存在，返回 0 。

```
impl Solution {
    /// 最大宽度坡
    ///
    /// 核心思路：将索引按对应元素值排序，使得遍历时元素值自动满足非递减条件，
    /// 然后只需追踪已遍历索引的最小值，计算当前索引与最小索引的差值即可得到宽度坡。
    ///
    /// 时间复杂度：O(n log n)（排序）
    /// 空间复杂度：O(n)（存储索引数组）
    pub fn max_width_ramp(nums: Vec<i32>) -> i32 {
        // 1. 创建索引数组 [0, 1, 2, ..., n-1]
        let mut indices: Vec<usize> = (0..nums.len()).collect();

        // 2. 将索引按照对应元素值升序排序
        // 排序后，对于任意 i < j，都有 nums[indices[i]] <= nums[indices[j]]
        indices.sort_by(|&i, &j| nums[i].cmp(&nums[j]));

        // 3. 初始化：最小索引和最大宽度
        // min_index 记录已遍历索引中的最小值（作为坡的起点）
        let mut min_index = indices[0];
        let mut max_width = 0;

        // 4. 遍历排序后的索引
        for &idx in &indices[1..] {
            // 当前索引与历史最小索引的差值 = 以当前为右端点的最大宽度坡
            // 由于元素值已满足非递减，只需保证索引顺序即可
            let width = idx as i32 - min_index as i32;
            max_width = max_width.max(width);

            // 更新历史最小索引，为后续元素提供更小的左边界可能
            min_index = min_index.min(idx);
        }

        max_width
    }
}
```
