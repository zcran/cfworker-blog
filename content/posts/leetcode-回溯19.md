---
title: "leetcode-回溯19"
date: 2026-07-04T10:22:02+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 组合总和 III

找出所有相加之和为 n 的 k 个数的组合，且满足下列条件：

只使用数字1到9

每个数字 最多使用一次

返回 所有可能的有效组合的列表 。该列表不能包含相同的组合两次，组合可以以任何顺序返回。


```
impl Solution {
    pub fn combination_sum3(k: i32, n: i32) -> Vec<Vec<i32>> {
        let mut result = Vec::new();
        let mut path = Vec::with_capacity(k as usize);
        Self::backtrack(1, k, n, &mut path, &mut result);
        result
    }

    /// 回溯搜索所有 k 个数之和为 n 的组合
    /// - start: 当前可选的起始数字（1~9）
    /// - k: 还需要选择的数字个数
    /// - remaining: 剩余需要凑齐的目标和
    /// - path: 当前已选的数字组合
    /// - result: 存储所有有效组合
    fn backtrack(start: i32, k: i32, remaining: i32, path: &mut Vec<i32>, result: &mut Vec<Vec<i32>>) {
        // 剪枝：剩余数字不足以凑够 k 个，或目标和为负数
        if k < 0 || remaining < 0 {
            return;
        }

        // 找到有效组合
        if k == 0 && remaining == 0 {
            result.push(path.clone());
            return;
        }

        // 剪枝：即使选择最小的 k 个数字也无法达到目标，或最大的 k 个数字也无法达到目标
        let min_sum = start * k + k * (k - 1) / 2;     // 从 start 开始连续 k 个数字的最小和
        let max_sum = (9 - k + 1 + 9) * k / 2;         // 最大的 k 个数字之和（从 9-k+1 到 9）
        if remaining < min_sum || remaining > max_sum {
            return;
        }

        // 枚举当前位置的选择
        for i in start..=9 {
            // 剪枝：如果当前数字已经超过剩余目标，后续更大的数字更不可能
            if i > remaining {
                break;
            }

            path.push(i);
            Self::backtrack(i + 1, k - 1, remaining - i, path, result);
            path.pop(); // 回溯
        }
    }
}
```
