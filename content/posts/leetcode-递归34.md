---
title: "leetcode-递归34"
date: 2026-05-26T10:00:18+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 等积子集的划分方案

给你一个整数数组 nums，其中包含的正整数 互不相同 ，另给你一个整数 target。

请判断是否可以将 nums 分成两个 非空、互不相交 的 子集 ，并且每个元素必须  恰好 属于 一个 子集，使得这两个子集中元素的乘积都等于 target。

如果存在这样的划分，返回 true；否则，返回 false。

子集 是数组中元素的一个选择集合。



```
impl Solution {
    /// 检查是否可以将数组划分为两个子集，使得两个子集的乘积都等于目标值
    ///
    /// # 问题说明
    /// 给定一个整数数组 `nums` 和一个目标值 `target`，判断是否能将数组分成两个子集
    /// （每个元素必须属于且仅属于其中一个子集），使得两个子集中所有元素的乘积都等于 `target`。
    ///
    /// # 算法思路
    /// 使用深度优先搜索（DFS）递归地尝试将每个元素分配到第一个子集或第二个子集，
    /// 同时跟踪两个子集的当前乘积。如果任一乘积超过目标值，则提前剪枝。
    ///
    /// # 复杂度
    /// - 时间复杂度：O(2^n) - 最坏情况下需要尝试所有分配方案
    /// - 空间复杂度：O(n) - 递归调用栈的深度
    ///
    /// # 参数
    /// - `nums`: 输入的整数数组
    /// - `target`: 目标乘积值
    ///
    /// # 返回
    /// - 如果存在一种划分方式使得两个子集的乘积都等于 target，返回 true；否则返回 false
    ///
    /// # 示例
    /// ```
    /// // 可以将 [3,1,6,8,4] 划分为 [3,1,8] (3*1*8=24) 和 [6,4] (6*4=24)
    /// assert_eq!(Solution::check_equal_partitions(vec![3,1,6,8,4], 24), true);
    ///
    /// // 无法将 [2,5,3,7] 划分为两个乘积都为 15 的子集
    /// assert_eq!(Solution::check_equal_partitions(vec![2,5,3,7], 15), false);
    /// ```
    pub fn check_equal_partitions(nums: Vec<i32>, target: i64) -> bool {
        let n = nums.len();

        /// 深度优先搜索递归函数
        ///
        /// # 参数
        /// - `index`: 当前处理的元素索引（从 0 开始）
        /// - `multi1`: 第一个子集的当前乘积
        /// - `multi2`: 第二个子集的当前乘积
        /// - `nums`: 原始数组的引用
        /// - `n`: 数组长度
        /// - `target`: 目标乘积值
        ///
        /// # 返回
        /// - 从当前状态开始，是否存在有效的划分
        fn dfs(
            index: usize,
            multi1: i64,
            multi2: i64,
            nums: &Vec<i32>,
            n: usize,
            target: i64,
        ) -> bool {
            // 剪枝优化：如果任一乘积已经超过目标值，则无需继续
            // 因为所有数字都是正数，乘积只会增加不会减少
            if multi1 > target || multi2 > target {
                return false;
            }

            // 基本情况：所有元素都分配完毕
            if index == n {
                // 检查两个子集的乘积是否都等于目标值
                return multi1 == target && multi2 == target;
            }

            // 递归尝试两种选择：
            // 1. 将当前元素分配给第一个子集
            // 2. 将当前元素分配给第二个子集
            // 只要有一种选择成功就返回 true
            dfs(
                index + 1,
                multi1 * nums[index] as i64,  // 将当前元素乘入第一个子集
                multi2,
                nums,
                n,
                target,
            ) || dfs(
                index + 1,
                multi1,
                multi2 * nums[index] as i64,  // 将当前元素乘入第二个子集
                nums,
                n,
                target,
            )
        }

        // 从第一个元素开始，两个子集的初始乘积都是 1（乘法单位元）
        dfs(0, 1, 1, &nums, n, target)
    }
}
```
