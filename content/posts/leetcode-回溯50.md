---
title: "leetcode-回溯50"
date: 2026-07-04T10:22:04+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 最接近目标价格的甜点成本

你打算做甜点，现在需要购买配料。目前共有 n 种冰激凌基料和 m 种配料可供选购。而制作甜点需要遵循以下几条规则：

· 必须选择 一种 冰激凌基料。
· 可以添加 一种或多种 配料，也可以不添加任何配料。
· 每种类型的配料 最多两份 。

给你以下三个输入：

· baseCosts ，一个长度为 n 的整数数组，其中每个 baseCosts[i] 表示第 i 种冰激凌基料的价格。
· toppingCosts，一个长度为 m 的整数数组，其中每个 toppingCosts[i] 表示 一份 第 i 种冰激凌配料的价格。
· target ，一个整数，表示你制作甜点的目标价格。

你希望自己做的甜点总成本尽可能接近目标价格 target 。

返回最接近 target 的甜点成本。如果有多种方案，返回 成本相对较低 的一种。


```
impl Solution {
    pub fn closest_cost(base_costs: Vec<i32>, topping_costs: Vec<i32>, target: i32) -> i32 {
        let mut best_cost = i32::MAX;
        let mut best_diff = i32::MAX;

        for &base in &base_costs {
            Self::backtrack(&topping_costs, 0, base, target, &mut best_cost, &mut best_diff);
        }

        best_cost
    }

    /// 回溯搜索所有配料组合
    /// - topping_costs: 配料价格列表
    /// - idx: 当前处理的配料索引
    /// - cost: 当前总成本
    /// - target: 目标价格
    /// - best_cost: 最接近目标的成本
    /// - best_diff: 最小差值
    fn backtrack(
        topping_costs: &[i32],
        idx: usize,
        cost: i32,
        target: i32,
        best_cost: &mut i32,
        best_diff: &mut i32,
    ) {
        let diff = (cost - target).abs();

        // 更新最优解：差值更小，或差值相同但成本更低
        if diff < *best_diff || (diff == *best_diff && cost < *best_cost) {
            *best_diff = diff;
            *best_cost = cost;
        }

        // 如果所有配料都已处理，或当前成本已超过目标（剪枝）
        if idx >= topping_costs.len() || cost > target + *best_diff {
            return;
        }

        // 不选当前配料
        Self::backtrack(topping_costs, idx + 1, cost, target, best_cost, best_diff);

        // 选一份当前配料
        Self::backtrack(
            topping_costs,
            idx + 1,
            cost + topping_costs[idx],
            target,
            best_cost,
            best_diff,
        );

        // 选两份当前配料
        Self::backtrack(
            topping_costs,
            idx + 1,
            cost + topping_costs[idx] * 2,
            target,
            best_cost,
            best_diff,
        );
    }
}
```
