---
title: "leetcode-有序集合1"
date: 2026-06-14T10:47:40+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 存在重复元素 III

给你一个整数数组 nums 和两个整数 indexDiff 和 valueDiff 。

找出满足下述条件的下标对 (i, j)：

· i != j,
· abs(i - j) <= indexDiff
· abs(nums[i] - nums[j]) <= valueDiff

如果存在，返回 true ；否则，返回 false 。


```
use std::collections::BTreeSet;

impl Solution {
    /// 判断数组中是否存在满足以下条件的下标对：
    /// 1. 下标差 <= index_diff
    /// 2. 数值差 <= value_diff
    ///
    /// # 算法思路
    /// 维护一个大小为 index_diff 的滑动窗口，使用 BTreeSet 有序存储窗口内元素。
    /// 对于每个新元素 x，在窗口中查找第一个 >= x - value_diff 的元素，
    /// 如果存在且 <= x + value_diff，则找到满足条件的对。
    ///
    /// # 复杂度
    /// - 时间复杂度: O(n log index_diff)，每个元素插入/删除/查找 O(log k)
    /// - 空间复杂度: O(index_diff)
    ///
    /// # 示例
    /// ```
    /// let nums = vec![1, 5, 9, 1, 5, 9];
    /// assert_eq!(Solution::contains_nearby_almost_duplicate(nums, 2, 3), false);
    /// ```
    pub fn contains_nearby_almost_duplicate(
        nums: Vec<i32>,
        index_diff: i32,
        value_diff: i32
    ) -> bool {
        // 边界优化：窗口大小为0或数值差为负数时直接返回false
        if index_diff <= 0 || value_diff < 0 || nums.len() < 2 {
            return false;
        }

        let index_diff = index_diff as usize;
        let mut window = BTreeSet::new();  // 有序集合，维护滑动窗口

        for (r, &num) in nums.iter().enumerate() {
            // 维护窗口大小：移除超出 index_diff 范围的旧元素
            if r > index_diff {
                window.remove(&nums[r - index_diff - 1]);
            }

            // 核心查找：在窗口中找第一个 >= num - value_diff 的元素
            // 如果该元素 <= num + value_diff，则差值满足条件
            let lower_bound = num - value_diff;
            if let Some(&candidate) = window.range(lower_bound..).next() {
                if candidate <= num + value_diff {
                    return true;
                }
            }

            // 将当前元素加入窗口
            window.insert(num);
        }

        false
    }
}
```
