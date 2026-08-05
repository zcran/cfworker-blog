---
title: "leetcode-回溯7"
date: 2026-07-04T10:22:01+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 全排列 II

给定一个可包含重复数字的序列 nums ，按任意顺序 返回所有不重复的全排列。


```
impl Solution {
    /// 全排列 II - 返回所有不重复的全排列
    ///
    /// # 示例
    /// ```
    /// 输入: nums = [1,1,2]
    /// 输出: [[1,1,2],[1,2,1],[2,1,1]]
    /// ```
    ///
    /// # 算法
    /// 回溯法 + 排序去重：
    /// 1. 排序使相同数字相邻
    /// 2. 使用 visited 数组标记已使用元素
    /// 3. 跳过重复数字：当 nums[i] == nums[i-1] 且前一个未使用时跳过
    ///
    /// # 复杂度
    /// - 时间复杂度: O(n * n!)，n! 个排列
    /// - 空间复杂度: O(n)，递归深度 + 辅助数组
    pub fn permute_unique(mut nums: Vec<i32>) -> Vec<Vec<i32>> {
        nums.sort_unstable();

        let mut result = Vec::new();
        let mut current = Vec::with_capacity(nums.len());
        let mut used = vec![false; nums.len()];

        Self::backtrack(&nums, &mut used, &mut current, &mut result);
        result
    }

    /// 回溯生成不重复排列
    fn backtrack(
        nums: &[i32],
        used: &mut [bool],
        current: &mut Vec<i32>,
        result: &mut Vec<Vec<i32>>,
    ) {
        // 所有元素都已使用，找到一个完整排列
        if current.len() == nums.len() {
            result.push(current.clone());
            return;
        }

        for i in 0..nums.len() {
            // 剪枝条件：
            // 1. 当前元素已使用，跳过
            if used[i] {
                continue;
            }

            // 2. 跳过重复元素：当 nums[i] == nums[i-1] 且前一个未使用时
            //    这保证了相同数字只能按顺序被使用，避免重复排列
            if i > 0 && nums[i] == nums[i - 1] && !used[i - 1] {
                continue;
            }

            // 选择当前元素
            used[i] = true;
            current.push(nums[i]);

            // 递归填充下一个位置
            Self::backtrack(nums, used, current, result);

            // 回溯：撤销选择
            used[i] = false;
            current.pop();
        }
    }
}
```
