---
title: "leetcode-堆69"
date: 2026-08-21T09:54:15+08:00
tags: ["leetcode", "堆"]
draft: false
---


## 购买最多物品数目 II

给你一个二维整数数组 items，其中 items[i] = [factorˇi, priceˇi] 表示下标为 i 的物品。同时给你一个整数 budget。

每种物品都有无限个可供购买。你可以购买任意数量的任意物品，但购买物品的总花费最多为 budget。

购买物品后，你可以根据以下规则获得免费的物品：

购买的每一份物品 i 最多 可以让你获得 一份 免费的其他物品 j。
免费物品必须满足 i != j 且 factorˇi 可以整除 factorˇj。
对于每个有序对 (i, j)，无论你购买了多少个物品 i，你从物品 i 的购买中 最多只能一次 免费获得物品 j。
如果免费物品 j 是通过购买不同种类的物品获得的，那么同一种物品 j 可以被免费获得多次。

返回你在购买物品花费最多为 budget 的前提下，能够获得的 物品最大总数 ，包括购买的物品和免费的物品。


```
impl Solution {
    /// 核心思路：将问题转化为「花钱买赠送资格」的贪心问题。
    ///
    /// 对于物品 i（factor = f, price = p）：
    /// - 买 1 份 i 可获赠 1 份其他物品 j（要求 f | factor_j）。
    /// - 但对固定的 (i, j) 组合，无论买多少份 i，最多只能从 i 免费得 1 份 j。
    /// - 因此买 k 份 i 最多获得 k 份免费物品，且这 k 份必须来自不同的 j。
    /// - 可选的 j 的数量 =「factor 是 f 的倍数的物品数」− 1（去掉自己）。
    ///
    /// 若 p ≥ 2·min_price，则「买 i 送 1」不如「直接买 2 个最便宜的」，直接跳过。
    /// 否则将 i 视为「赠送者」：花 p 元可得到 2 份物品，按价格升序贪心选取。
    /// 最后剩余预算全部购买最便宜的物品。
    pub fn maximum_sale_items(items: Vec<Vec<i32>>, mut budget: i32) -> i32 {
        let n = items.len();

        // 统计每个 factor 出现次数，同时找最低单价
        let mut factor_cnt = vec![0i32; n + 1];
        let mut min_price = i32::MAX;
        for item in &items {
            let f = item[0] as usize;
            let p = item[1];
            factor_cnt[f] += 1;
            min_price = min_price.min(p);
        }

        // 每个 factor 对应的「可赠送目标数」（即倍数个数 − 1）
        // 用 -1 作为「尚未计算」的标记
        let mut gift_cnt = vec![-1i32; n + 1];
        let mut candidates = Vec::new();

        for item in &items {
            let f = item[0] as usize;
            let p = item[1];

            // 性价比不足，跳过
            if p >= min_price * 2 {
                continue;
            }

            // 懒计算：每个 factor 只算一次倍数个数
            if gift_cnt[f] == -1 {
                let mut cnt = 0;
                for multiple in (f..=n).step_by(f) {
                    cnt += factor_cnt[multiple];
                }
                gift_cnt[f] = cnt - 1; // 去掉自己
            }

            if gift_cnt[f] > 0 {
                candidates.push((p, gift_cnt[f]));
            }
        }

        candidates.sort_unstable_by_key(|&(price, _)| price);

        let mut ans = 0;
        for (price, max_gifts) in candidates {
            if budget < price {
                break;
            }
            let buy = (budget / price).min(max_gifts);
            budget -= price * buy;
            ans += buy * 2;
        }

        // 剩余预算买最便宜的（1 元 1 份，无赠送）
        ans += budget / min_price;
        ans
    }
}
```
