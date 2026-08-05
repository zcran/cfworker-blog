---
title: "leetcode-回溯5"
date: 2026-07-04T10:22:01+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 组合总和 II


给定一个候选人编号的集合 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。

candidates 中的每个数字在每个组合中只能使用 一次 。

注意：解集不能包含重复的组合。

```
impl Solution {
    /// 组合总和 II - 找出所有和为目标值的数字组合（每个数字只能使用一次）
    ///
    /// # 示例
    /// ```
    /// 输入: candidates = [10,1,2,7,6,1,5], target = 8
    /// 输出: [[1,1,6], [1,2,5], [1,7], [2,6]]
    /// ```
    ///
    /// # 算法
    /// 回溯法 + 频率计数：
    /// 1. 统计每个数字的出现次数
    /// 2. 对每个数字，枚举使用的次数（0 到 min(频率, target/数字)）
    /// 3. 递归处理下一个不同数字
    ///
    /// # 复杂度
    /// - 时间复杂度: O(2^n)
    /// - 空间复杂度: O(n)
    pub fn combination_sum2(mut candidates: Vec<i32>, target: i32) -> Vec<Vec<i32>> {
        // 排序以便统计频率
        candidates.sort_unstable();

        // 统计每个数字及其出现次数
        let mut freq: Vec<(i32, i32)> = Vec::new();
        for &num in &candidates {
            if let Some(last) = freq.last_mut() {
                if last.0 == num {
                    last.1 += 1;
                    continue;
                }
            }
            freq.push((num, 1));
        }

        let mut result = Vec::new();
        let mut current = Vec::new();

        Self::backtrack(&freq, target, 0, &mut current, &mut result);
        result
    }

    /// 回溯搜索函数
    fn backtrack(
        freq: &[(i32, i32)],
        remain: i32,
        pos: usize,
        current: &mut Vec<i32>,
        result: &mut Vec<Vec<i32>>,
    ) {
        // 找到有效组合
        if remain == 0 {
            result.push(current.clone());
            return;
        }

        // 剪枝：无更多数字或当前数字已经大于剩余值
        if pos == freq.len() || remain < freq[pos].0 {
            return;
        }

        let (num, count) = freq[pos];

        // 情况 1：不选择当前数字
        Self::backtrack(freq, remain, pos + 1, current, result);

        // 情况 2：选择当前数字 1 到 max_count 次
        // 计算最多可以选择多少次（不能超过频率，也不能超过剩余值）
        let max_use = (remain / num).min(count) as usize;

        // 尝试选择 1 到 max_use 次
        let mut sum = 0;
        for _ in 1..=max_use {
            sum += num;
            current.push(num);
            Self::backtrack(freq, remain - sum, pos + 1, current, result);
        }

        // 回溯：移除添加的 num
        for _ in 0..max_use {
            current.pop();
        }
    }
}
```
