---
title: "leetcode-位运算48"
date: 2026-09-24T10:33:37+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 使子序列的和等于目标的最少操作次数

给你一个下标从 0 开始的数组 nums ，它包含 非负 整数，且全部为 2 的幂，同时给你一个整数 target 。

一次操作中，你必须对数组做以下修改：

选择数组中一个元素 nums[i] ，满足 nums[i] > 1 。
将 nums[i] 从数组中删除。
在 nums 的 末尾 添加 两个 数，值都为 nums[i] / 2 。

你的目标是让 nums 的一个 子序列 的元素和等于 target ，请你返回达成这一目标的 最少操作次数 。如果无法得到这样的子序列，请你返回 -1 。

数组中一个 子序列 是通过删除原数组中一些元素，并且不改变剩余元素顺序得到的剩余数组。


```
use std::collections::HashMap;

impl Solution {
    // 把 target 按二进制拆成若干个 2 的幂（每个为 1 的位对应一个需求）。
    fn resolve(target: i64) -> Vec<i64> {
        (0..31)
            .filter(|&i| target & (1 << i) != 0)
            .map(|i| 1 << i)
            .collect()
    }

    // 尝试用现有数字直接凑出 target（从大到小贪心分解），成功则扣除并返回 true。
    fn try_sum_to_target(target: i64, map: &mut HashMap<i64, i32>) -> bool {
        let mut needs: Vec<(i64, i32)> = Vec::new();
        let mut need = target;
        let mut count = 1;
        // 从 target 向下逐层找可用的 2 的幂，不足则继续二分需求。
        while need != 0 {
            let value = map.entry(need).or_insert(0);
            if *value < count {
                count -= *value;
                needs.push((need, *value));
                need /= 2;
                count *= 2;
            } else {
                needs.push((need, count));
                break;
            }
        }
        if need == 0 {
            return false;
        }
        // 扣除实际使用的数字。
        for (need, count) in needs {
            *map.entry(need).or_insert(0) -= count;
        }
        true
    }

    // 找不到现成数字时，把某个更大的 2 的幂不断二分来凑出 target，返回额外操作数。
    fn try_resolve_to_target(target: i64, map: &mut HashMap<i64, i32>) -> Option<i32> {
        let mut bigger: i64 = -1;
        // 找一个比 target 大的、当前数量不为 0 的 2 的幂。
        for offset in 0..31 {
            if (1 << offset) < target {
                continue;
            }
            let value = map.entry(1 << offset).or_insert(0);
            if *value != 0 {
                *value -= 1;
                bigger = 1 << offset;
                break;
            }
        }
        if bigger == -1 {
            return None;
        }
        // 不断二分直到等于 target，每次二分记一次操作。
        let mut result = 0;
        while bigger != target {
            bigger /= 2;
            result += 1;
            *map.entry(bigger).or_insert(0) += 1;
        }
        Some(result)
    }

    pub fn min_operations(nums: Vec<i32>, target: i32) -> i32 {
        let target = target as i64;
        if nums.iter().map(|&x| x as i64).sum::<i64>() < target {
            return -1;
        }

        // 统计每个 2 的幂出现的次数。
        let mut map: HashMap<i64, i32> = HashMap::new();
        for num in nums {
            *map.entry(num as i64).or_insert(0) += 1;
        }

        let mut result = 0;
        // 逐位处理 target 的需求。
        for need in Self::resolve(target) {
            if Self::try_sum_to_target(need, &mut map) {
                continue;
            }
            match Self::try_resolve_to_target(need, &mut map) {
                Some(step) => result += step,
                None => return -1,
            }
        }
        result
    }
}
```
