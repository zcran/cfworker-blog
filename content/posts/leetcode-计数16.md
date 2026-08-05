---
title: "leetcode-计数16"
date: 2026-08-01T10:40:51+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 等价多米诺骨牌对的数量

给你一组多米诺骨牌 dominoes 。

形式上，dominoes[i] = [a, b] 与 dominoes[j] = [c, d] 等价 当且仅当 (a == c 且 b == d) 或者 (a == d 且 b == c) 。即一张骨牌可以通过旋转 0 度或 180 度得到另一张多米诺骨牌。

在 0 <= i < j < dominoes.length 的前提下，找出满足 dominoes[i] 和 dominoes[j] 等价的骨牌对 (i, j) 的数量。


```
impl Solution {
    pub fn num_equiv_domino_pairs(dominoes: Vec<Vec<i32>>) -> i32 {
        // 使用二维数组统计每个有序对出现的次数（数值范围 1-9）
        let mut count = [[0; 10]; 10];
        let mut pairs = 0;

        for domino in dominoes {
            // 获取两个数字并归一化：较小的在前，较大的在后
            let (a, b) = if domino[0] < domino[1] {
                (domino[0] as usize, domino[1] as usize)
            } else {
                (domino[1] as usize, domino[0] as usize)
            };

            // 当前多米诺骨牌与之前所有相同类型骨牌配对
            pairs += count[a][b];
            count[a][b] += 1;
        }

        pairs
    }
}
```
