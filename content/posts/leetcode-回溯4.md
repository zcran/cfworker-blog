---
title: "leetcode-回溯4"
date: 2026-07-04T10:22:01+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 组合总和

给你一个 无重复元素 的整数数组 candidates 和一个目标整数 target ，找出 candidates 中可以使数字和为目标数 target 的 所有 不同组合 ，并以列表形式返回。你可以按 任意顺序 返回这些组合。

candidates 中的 同一个 数字可以 无限制重复被选取 。如果至少一个数字的被选数量不同，则两种组合是不同的。

对于给定的输入，保证和为 target 的不同组合数少于 150 个。


```
impl Solution {
    /// 组合总和 - 找出所有和为目标值的数字组合（可重复使用同一数字）
    ///
    /// # 示例
    /// ```
    /// 输入: candidates = [2,3,6,7], target = 7
    /// 输出: [[2,2,3], [7]]
    /// ```
    ///
    /// # 算法
    /// 回溯法 + 剪枝优化：
    /// - 先排序，利用有序性提前终止无效搜索
    /// - 从当前索引开始，避免重复组合（保证组合递增）
    ///
    /// # 复杂度
    /// - 时间复杂度: O(2^n)，但实际远小于此（剪枝）
    /// - 空间复杂度: O(target/min(candidates))，递归深度
    pub fn combination_sum(mut candidates: Vec<i32>, target: i32) -> Vec<Vec<i32>> {
        // 排序是剪枝的前提
        candidates.sort_unstable();

        let mut result = Vec::new();
        let mut current = Vec::new();

        Self::backtrack(&candidates, target, 0, &mut current, &mut result);
        result
    }

    /// 回溯搜索函数
    ///
    /// # 参数
    /// - `candidates`: 已排序的候选数字列表
    /// - `remain`: 剩余需要凑齐的目标值
    /// - `start`: 当前搜索的起始索引（避免重复组合）
    /// - `current`: 当前构建的组合
    /// - `result`: 存储所有有效组合
    fn backtrack(
        candidates: &[i32],
        remain: i32,
        start: usize,
        current: &mut Vec<i32>,
        result: &mut Vec<Vec<i32>>,
    ) {
        // 找到有效组合
        if remain == 0 {
            result.push(current.clone());
            return;
        }

        // 剪枝：从 start 开始遍历
        for i in start..candidates.len() {
            let num = candidates[i];

            // 剪枝：由于数组已排序，当前数字超过剩余值，后续更大数字无需检查
            if num > remain {
                break;
            }

            // 选择当前数字
            current.push(num);

            // 递归：因为数字可重复使用，所以 start 仍为 i（不是 i+1）
            Self::backtrack(candidates, remain - num, i, current, result);

            // 回溯：撤销选择
            current.pop();
        }
    }
}
```
