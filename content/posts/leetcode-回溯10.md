---
title: "leetcode-回溯10"
date: 2026-07-04T10:22:02+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 组合

给定两个整数 n 和 k，返回范围 [1, n] 中所有可能的 k 个数的组合。

你可以按 任何顺序 返回答案。


```
impl Solution {
    pub fn combine(n: i32, k: i32) -> Vec<Vec<i32>> {
        let mut result = Vec::new();
        let mut current = Vec::with_capacity(k as usize);

        // 从数字 1 开始深度优先搜索
        Self::dfs(1, n, k, &mut current, &mut result);
        result
    }

    /// 深度优先搜索生成组合
    /// - start: 当前可选的起始数字
    /// - n: 数字范围上限
    /// - k: 需要选择的数字个数
    /// - current: 当前正在构建的组合
    /// - result: 存储所有有效组合
    fn dfs(start: i32, n: i32, k: i32, current: &mut Vec<i32>, result: &mut Vec<Vec<i32>>) {
        // 剪枝：剩余可选的数字不足以凑齐 k 个
        let remaining = k as usize - current.len();
        if remaining > (n - start + 1) as usize {
            return;
        }

        // 找到一个有效组合
        if current.len() == k as usize {
            result.push(current.clone());
            return;
        }

        // 优化：只遍历到 n - remaining + 1，避免不必要的递归
        for i in start..=n - remaining as i32 + 1 {
            current.push(i);
            Self::dfs(i + 1, n, k, current, result);
            current.pop(); // 回溯
        }
    }
}
```
