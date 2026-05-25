---
title: "leetcode-树状树组9"
date: 2026-05-03T20:07:52+08:00
tags: ["leetcode", "树状树组"]
draft: false
---


## 统计作战单位数

n 名士兵站成一排。每个士兵都有一个 独一无二 的评分 rating 。

从中选出 3 个士兵组成一个作战单位，规则如下：

从队伍中选出下标分别为 i、j、k 的 3 名士兵，他们的评分分别为 rating[i]、rating[j]、rating[k]
作战单位需满足： rating[i] < rating[j] < rating[k] 或者 rating[i] > rating[j] > rating[k] ，其中  0 <= i < j < k < n
请你返回按上述条件组建的作战单位的方案数。

```
impl Solution {
    /// 计算可组成多少个升序或降序的三人小队
    /// 算法：枚举中间队员的位置 j，统计其左边小于/大于它的数量，右边小于/大于它的数量，
    ///       然后根据组合原理累加：左边小×右边大（升序） + 左边大×右边小（降序）
    pub fn num_teams(rating: Vec<i32>) -> i32 {
        (1..rating.len() - 1) // 中间队员不能是第一个或最后一个
            .map(|j| {
                let mid = rating[j];
                let (left, right) = (&rating[..j], &rating[j + 1..]);

                let left_less  = left.iter().filter(|&&x| x < mid).count();
                let left_greater = left.len() - left_less;

                let right_less = right.iter().filter(|&&x| x < mid).count();
                let right_greater = right.len() - right_less;

                // 升序组合 (小, 中, 大) + 降序组合 (大, 中, 小)
                (left_less * right_greater + left_greater * right_less) as i32
            })
            .sum()
    }
}
```
