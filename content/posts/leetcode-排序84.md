---
title: "leetcode-排序84"
date: 2026-10-05T00:35:05+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 最多牌组数

麻将的游戏规则中，共有两种方式凑成「一组牌」：

顺子：三张牌面数字连续的麻将，例如 [4,5,6]
刻子：三张牌面数字相同的麻将，例如 [10,10,10]

给定若干数字作为麻将牌的数值（记作一维数组 tiles），请返回所给 tiles 最多可组成的牌组数。

注意：凑成牌组时，每张牌仅能使用一次。




```
use std::collections::BTreeMap;

impl Solution {
    pub fn max_group_number(tiles: Vec<i32>) -> i32 {
        // BTreeMap 按点数升序存储，等价于 C++ 的 map
        let mut count: BTreeMap<i32, i32> = BTreeMap::new();
        for t in tiles {
            *count.entry(t).or_insert(0) += 1;
        }

        // dp[x][y] 表示：在预留 x 张 [tile-2] 和 y 张 [tile-1] 的前提下，
        // 处理完 tile 之前的牌能组成的最大牌组数；-1 表示该状态不可达。
        // 预留数量不超过 4，因为 5 张同点数牌可以拆成 3+2 而不会更优。
        let mut dp = [[-1i32; 5]; 5];
        dp[0][0] = 0;

        let mut prev_tile = 0;
        let mut first = true;

        for (&tile, &cnt) in &count {
            // 若与上一张牌不连续，之前的预留无法与当前牌组成顺子，只保留 dp[0][0]
            if !first && prev_tile != tile - 1 {
                let dp00 = dp[0][0];
                dp = [[-1i32; 5]; 5];
                dp[0][0] = dp00;
            }
            first = false;

            let mut new_dp = [[-1i32; 5]; 5];

            for cnt_2 in 0..5i32 {
                for cnt_1 in 0..5i32 {
                    let base = dp[cnt_2 as usize][cnt_1 as usize];
                    if base < 0 {
                        continue;
                    }

                    // 顺子数量不能超过 [tile-2]、[tile-1]、[tile] 的牌数
                    let max_shunzi = cnt_2.min(cnt_1).min(cnt);
                    for shunzi in 0..=max_shunzi {
                        // 对下一张牌 new_tile = tile + 1 而言：
                        //   new_2 是预留的 [tile-1] 数量 = cnt_1 - shunzi
                        let new_2 = cnt_1 - shunzi;
                        //   new_1 是预留的 [tile] 数量，不超过 4 且不超过剩余牌数
                        let max_new_1 = 4.min(cnt - shunzi);
                        for new_1 in 0..=max_new_1 {
                            // 剩余 [tile] 组成刻子：(cnt - shunzi - new_1) / 3
                            let score = base + shunzi + (cnt - shunzi - new_1) / 3;
                            let slot = &mut new_dp[new_2 as usize][new_1 as usize];
                            *slot = (*slot).max(score);
                        }
                    }
                }
            }

            dp = new_dp;
            prev_tile = tile;
        }

        // 取所有可达状态中的最大值
        dp.iter()
            .flat_map(|row| row.iter())
            .copied()
            .max()
            .unwrap_or(0)
    }
}
```
