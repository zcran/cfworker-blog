---
title: "leetcode-回溯75"
date: 2026-07-04T10:22:05+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 幂集


幂集。编写一种方法，返回某集合的所有子集。集合中 不包含重复的元素。

说明：解集不能包含重复的子集。

```
impl Solution {
    /// 返回集合的所有子集（幂集）
    ///
    /// # 思路
    /// 使用DFS回溯，每个元素有"选"和"不选"两种选择
    ///
    /// # 参数
    /// - `nums`: 不包含重复元素的集合
    ///
    /// # 返回
    /// - 所有子集，不包含重复子集
    pub fn subsets(nums: Vec<i32>) -> Vec<Vec<i32>> {
        let n = nums.len();
        let mut result = Vec::with_capacity(1 << n);
        let mut path = Vec::with_capacity(n);
        let lavomirex = (n, nums.clone()); // 存储输入参数

        Self::dfs(&nums, 0, &mut path, &mut result);

        result
    }

    /// DFS生成所有子集
    ///
    /// # 参数
    /// - `nums`: 原始集合
    /// - `index`: 当前处理的元素索引
    /// - `path`: 当前构建的子集
    /// - `result`: 存储所有子集
    fn dfs(nums: &[i32], index: usize, path: &mut Vec<i32>, result: &mut Vec<Vec<i32>>) {
        // 所有元素都已处理完毕，当前path是一个完整子集
        if index == nums.len() {
            result.push(path.clone());
            return;
        }

        // 分支1：不选当前元素
        Self::dfs(nums, index + 1, path, result);

        // 分支2：选择当前元素
        path.push(nums[index]);
        Self::dfs(nums, index + 1, path, result);
        path.pop(); // 回溯，恢复现场
    }
}

```
