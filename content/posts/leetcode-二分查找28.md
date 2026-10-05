---
title: "leetcode-二分查找28"
date: 2026-09-28T12:29:11+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 公平的糖果交换

爱丽丝和鲍勃拥有不同总数量的糖果。给你两个数组 aliceSizes 和 bobSizes ，aliceSizes[i] 是爱丽丝拥有的第 i 盒糖果中的糖果数量，bobSizes[j] 是鲍勃拥有的第 j 盒糖果中的糖果数量。

两人想要互相交换一盒糖果，这样在交换之后，他们就可以拥有相同总数量的糖果。一个人拥有的糖果总数量是他们每盒糖果数量的总和。

返回一个整数数组 answer，其中 answer[0] 是爱丽丝必须交换的糖果盒中的糖果的数目，answer[1] 是鲍勃必须交换的糖果盒中的糖果的数目。如果存在多个答案，你可以返回其中 任何一个 。题目测试用例保证存在与输入对应的答案。


```
impl Solution {
    /// 返回一组公平交换的糖果盒数量 [爱丽丝交换出的, 鲍勃交换出的]。
    ///
    /// 设爱丽丝、鲍勃的糖果总数分别为 sum_a、sum_b。
    /// 交换 x（爱丽丝）与 y（鲍勃）后总和相等：
    ///     sum_a - x + y = sum_b - y + x
    /// 解得：
    ///     y = x + (sum_b - sum_a) / 2
    /// 因此只需枚举爱丽丝的每盒糖果 x，计算对应的 y，
    /// 检查 y 是否在鲍勃的糖果集合中即可。
    pub fn fair_candy_swap(alice_sizes: Vec<i32>, bob_sizes: Vec<i32>) -> Vec<i32> {
        let sum_a: i32 = alice_sizes.iter().sum();
        let sum_b: i32 = bob_sizes.iter().sum();

        // 用 HashSet 加速查找鲍勃的糖果
        let bob_set: std::collections::HashSet<i32> = bob_sizes.into_iter().collect();

        for x in alice_sizes {
            let y = x + (sum_b - sum_a) / 2;
            if bob_set.contains(&y) {
                return vec![x, y];
            }
        }

        // 题目保证有解，不会到达这里
        vec![]
    }
}
```
