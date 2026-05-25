---
title: "leetcode-树状树组14"
date: 2026-05-03T20:07:52+08:00
tags: ["leetcode", "树状树组"]
draft: false
---


## 找出到每个位置为止最长的有效障碍赛跑路线

你打算构建一些障碍赛跑路线。给你一个 下标从 0 开始 的整数数组 obstacles ，数组长度为 n ，其中 obstacles[i] 表示第 i 个障碍的高度。

对于每个介于 0 和 n - 1 之间（包含 0 和 n - 1）的下标  i ，在满足下述条件的前提下，请你找出 obstacles 能构成的最长障碍路线的长度：

·你可以选择下标介于 0 到 i 之间（包含 0 和 i）的任意个障碍。
·在这条路线中，必须包含第 i 个障碍。
·你必须按障碍在 obstacles 中的 出现顺序 布置这些障碍。
·除第一个障碍外，路线中每个障碍的高度都必须和前一个障碍 相同 或者 更高 。

返回长度为 n 的答案数组 ans ，其中 ans[i] 是上面所述的下标 i 对应的最长障碍赛跑路线的长度。


```
use std::cmp::Ordering;

impl Solution {
    /// 计算每个位置的最长非递减子序列长度（障碍赛跑问题）。
    ///
    /// 对于每个位置 i，以 obstacles[i] 结尾的最长非递减子序列的长度等于
    /// 在之前所有元素中，第一个大于 obstacles[i] 的位置索引 + 1。
    ///
    /// 维护一个 `tails` 数组，其中 `tails[j]` 表示长度为 j+1 的递增子序列的**最小末尾值**。
    /// 对于当前障碍物高度 `h`：
    /// - 在 `tails` 中二分查找第一个**大于** `h` 的位置（即 `tails[pos] > h`）。
    /// - 若找不到（所有元素都 ≤ h），则 `pos == tails.len()`，将 `h` 追加到末尾。
    /// - 否则，用 `h` 替换 `tails[pos]`（因为更小的末尾值更利于后续扩展）。
    /// - 结果即为 `pos + 1`（因为 `tails[0..pos]` 中的元素都 ≤ h，所以以 h 结尾的最长长度是 pos+1）。
    pub fn longest_obstacle_course_at_each_position(obstacles: Vec<i32>) -> Vec<i32> {
        let mut tails = Vec::new();     // 维护各个长度的最小末尾值
        let mut result = Vec::with_capacity(obstacles.len());

        for &height in &obstacles {
            // 二分查找第一个大于 height 的位置（默认为右边界）
            let pos = tails.binary_search_by(|&x| {
                if x <= height {
                    Ordering::Less    // 继续向右找
                } else {
                    Ordering::Greater // 当前元素 > height，向左收缩
                }
            }).unwrap_or_else(|p| p);  // 返回 Err(p) 即为插入点

            // 更新 tails 数组：在 pos 处放置 height（若 pos == len 则追加）
            if pos == tails.len() {
                tails.push(height);
            } else {
                tails[pos] = height;
            }

            // 当前以 height 结尾的最长非递减子序列长度 = pos + 1
            result.push((pos + 1) as i32);
        }

        result
    }
}
```
