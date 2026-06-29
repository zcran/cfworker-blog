---
title: "leetcode-线段树12"
date: 2026-06-19T09:37:24+08:00
tags: ["leetcode", "线段树"]
draft: false
---


## 最大子数组总值 II

给你一个长度为 n 的整数数组 nums 和一个整数 k。

你必须从 nums 中选择 恰好 k 个 不同 的 子数组 nums[l..r]。子数组可以重叠，但同一个子数组（相同的 l 和 r）不能 被选择超过一次。

子数组 nums[l..r] 的 值 定义为：max(nums[l..r]) - min(nums[l..r])。

总值 是所有被选子数组的 值 之和。

返回你能实现的 最大 可能总值。

子数组 是数组中连续的 非空 元素序列。


```
use std::collections::BinaryHeap;

impl Solution {
    /// 选择恰好 k 个不同子数组，最大化它们的 (max - min) 之和
    ///
    /// 核心思路：
    /// 1. 使用单调栈计算每个元素作为最大值/最小值的贡献边界
    /// 2. 将数组排序，用优先队列按差值从大到小处理
    /// 3. 批量计数相同差值的子数组，避免逐个枚举
    ///
    /// 时间复杂度 O(n log n + k)，空间复杂度 O(n)
    pub fn max_total_value(nums: Vec<i32>, k: i32) -> i64 {
        let n = nums.len();

        // ---------- 单调栈：计算每个元素的贡献边界 ----------
        // next_g[i]: 右边第一个 >= nums[i] 的位置（最大值去重）
        // next_l[i]: 右边第一个 < nums[i] 的位置（最小值去重）
        // prev_g[i]: 左边第一个 > nums[i] 的位置
        // prev_l[i]: 左边第一个 <= nums[i] 的位置
        let mut next_g = vec![n as i32; n];
        let mut next_l = vec![n as i32; n];
        let mut prev_g = vec![-1; n];
        let mut prev_l = vec![-1; n];

        // 单调栈：计算最大值的 next_g 和 prev_g
        let mut stack = Vec::new();
        for i in 0..n {
            // 弹出所有 <= nums[i] 的元素，它们右边第一个 >= 自己的位置就是 i
            while let Some(&top) = stack.last() {
                if nums[top] <= nums[i] {
                    next_g[top] = i as i32;
                    stack.pop();
                } else {
                    break;
                }
            }
            // 栈顶是左边第一个 > nums[i] 的位置
            if let Some(&top) = stack.last() {
                prev_g[i] = top as i32;
            }
            stack.push(i);
        }

        // 单调栈：计算最小值的 next_l 和 prev_l
        stack.clear();
        for i in 0..n {
            // 弹出所有 > nums[i] 的元素，它们右边第一个 < 自己的位置就是 i
            while let Some(&top) = stack.last() {
                if nums[top] > nums[i] {
                    next_l[top] = i as i32;
                    stack.pop();
                } else {
                    break;
                }
            }
            // 栈顶是左边第一个 <= nums[i] 的位置
            if let Some(&top) = stack.last() {
                prev_l[i] = top as i32;
            }
            stack.push(i);
        }

        // ---------- 排序并建立优先队列 ----------
        // 将 (值, 原始索引) 按值升序排列
        let mut sorted: Vec<(i32, usize)> = nums
            .into_iter()
            .enumerate()
            .map(|(i, v)| (v, i))
            .collect();
        sorted.sort_unstable();

        // 优先队列存储：(差值, 最小值的排序索引, 最大值的排序索引)
        // 差值 = sorted[r].0 - sorted[l].0
        let mut heap = BinaryHeap::new();
        heap.push((sorted[n - 1].0 - sorted[0].0, 0, n - 1));

        let mut ans = 0i64;
        let mut remaining = k as i64;

        // ---------- 贪心选取 ----------
        while let Some((diff, l, r)) = heap.pop() {
            // 差值为 0 或已选够，提前结束
            if diff == 0 || remaining <= 0 {
                break;
            }

            // 当前状态：最小值为 sorted[l]，最大值为 sorted[r]
            let min_idx = sorted[l].1;
            let max_idx = sorted[r].1;

            // 计算以 (min_idx, max_idx) 为极值的子数组数量
            // 右边界限制：最大值右边第一个 >= 自己的位置，和最小值右边第一个 < 自己的位置，取较小值
            let right_bound = next_g[max_idx].min(next_l[min_idx]);
            // 左边界限制：最大值左边第一个 > 自己的位置，和最小值左边第一个 <= 自己的位置，取较大值
            let left_bound = prev_g[max_idx].max(prev_l[min_idx]);

            // 子数组必须包含 min_idx 和 max_idx
            let left = min_idx.min(max_idx) as i32;
            let right = min_idx.max(max_idx) as i32;

            // 可选的左端点数：从 left_bound + 1 到 left
            let left_count = (left - left_bound) as i64;
            // 可选的右端点数：从 right 到 right_bound - 1
            let right_count = (right_bound - right) as i64;

            // 批量选取相同差值的子数组
            if left_count > 0 && right_count > 0 {
                let total_count = left_count * right_count;
                let take = total_count.min(remaining);
                ans += take * diff as i64;
                remaining -= take;
            }

            if remaining <= 0 {
                break;
            }

            // 将当前状态拆分为两个子状态继续探索
            // 注意：这里的拆分逻辑必须与原代码一致
            // 1. 如果 l == 0，说明当前最小值已经是全局最小值，只能缩小最大值
            if l == 0 {
                if l <= r - 1 {
                    let new_diff = sorted[r - 1].0 - sorted[l].0;
                    heap.push((new_diff, l, r - 1));
                }
            }
            // 2. 增大最小值（左边界右移）
            if l + 1 <= r {
                let new_diff = sorted[r].0 - sorted[l + 1].0;
                heap.push((new_diff, l + 1, r));
            }
        }

        ans
    }
}
```
