---
title: "leetcode-回溯6"
date: 2026-07-04T10:22:01+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 全排列

给定一个不含重复数字的数组 nums ，返回其 所有可能的全排列 。你可以 按任意顺序 返回答案。


```
impl Solution {
    /// 全排列 - 返回所有可能的排列
    ///
    /// # 示例
    /// ```
    /// 输入: nums = [1,2,3]
    /// 输出: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
    /// ```
    ///
    /// # 算法
    /// 回溯法：通过交换元素生成排列，避免额外数组
    ///
    /// # 复杂度
    /// - 时间复杂度: O(n * n!)，n! 个排列，每个排列需要 O(n) 复制
    /// - 空间复杂度: O(n)，递归深度
    pub fn permute(mut nums: Vec<i32>) -> Vec<Vec<i32>> {
        let mut result = Vec::new();
        Self::backtrack(&mut nums, 0, &mut result);
        result
    }

    /// 回溯生成排列（原地交换法）
    ///
    /// # 参数
    /// - `nums`: 当前排列（通过交换生成）
    /// - `start`: 当前填充位置
    /// - `result`: 存储所有排列
    fn backtrack(nums: &mut [i32], start: usize, result: &mut Vec<Vec<i32>>) {
        // 所有位置都已填充，找到一个完整排列
        if start == nums.len() {
            result.push(nums.to_vec());
            return;
        }

        // 将 start 位置的元素与 start..n-1 的元素交换
        for i in start..nums.len() {
            // 交换元素，将 nums[i] 放到当前位置
            nums.swap(start, i);

            // 递归填充下一个位置
            Self::backtrack(nums, start + 1, result);

            // 回溯：恢复交换，尝试下一个元素
            nums.swap(start, i);
        }
    }
}
```
