---
title: "leetcode-树状树组21"
date: 2026-05-03T20:07:53+08:00
tags: ["leetcode", "树状树组"]
draft: false
---


## 将数组清空

给你一个包含若干 互不相同 整数的数组 nums ，你需要执行以下操作 直到数组为空 ：

·如果数组中第一个元素是当前数组中的 最小值 ，则删除它。
·否则，将第一个元素移动到数组的 末尾 。

请你返回需要多少个操作使 nums 为空。

```
impl Solution {
    /// 计算将数组清空所需的最小操作次数。
    /// 操作规则：每次检查第一个元素，若它是当前最小值则移除，否则将其移到末尾。
    ///
    /// 算法原理：
    /// 1. 记录每个元素在原数组中的索引。
    /// 2. 按元素值升序对索引排序，得到处理顺序（即每次应移除的元素位置）。
    /// 3. 遍历排序后的索引序列，若当前索引小于前一个索引，说明需要多一轮循环，
    ///    此时额外增加 `剩余未处理元素个数` 次操作。
    ///
    /// 时间复杂度 O(n log n)，空间复杂度 O(n)。
    pub fn count_operations_to_empty_array(nums: Vec<i32>) -> i64 {
        let n = nums.len() as i64;
        // 初始操作次数：至少需要 n 次移除（每个元素一次）
        let mut operations = n;

        // 获取按值排序后的原索引（保持值相同的相对顺序不变）
        let mut sorted_indices: Vec<usize> = (0..n as usize).collect();
        sorted_indices.sort_by_key(|&idx| nums[idx]);

        // 遍历排序后的索引，统计因顺序逆转而需要额外增加的轮次
        for i in 1..n as usize {
            if sorted_indices[i] < sorted_indices[i - 1] {
                // 当前索引小于前一个，意味着在原始数组中需要绕一圈才能遇到它
                // 此时增加 (n - i) 次操作（即还未处理的元素个数）
                operations += n - i as i64;
            }
        }

        operations
    }
}
```
