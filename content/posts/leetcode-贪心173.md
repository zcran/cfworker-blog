---
title: "leetcode-贪心173"
date: 2026-10-03T12:55:45+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 购买最多物品数目 I

给你一个二维整数数组 items，其中 items[i] = [factori, pricei] 表示下标为 i 的物品。同时给你一个整数 budget。

每种物品都有无限个可供购买。你可以购买任意数量的任意物品，但购买物品的总花费最多为 budget。

购买物品后，你可以据以下规则获得免费的物品：

· 如果你购买了若干个物品 i，所有满足 j != i 且 factori 可以整除 factorj 的物品 j ，你都能 免费 获得一份。
· 重复购买物品 i 不能 再获取额外的免费物品。
· 如果免费物品 j 是通过购买不同种类的物品获得的，那么同一种物品 j 可以被免费获得多次。

返回你在购买物品花费最多为 budget 的前提下，能够获得的 物品最大总数 ，包括购买的物品和免费的物品。


```
impl Solution {
    pub fn maximum_sale_items(items: Vec<Vec<i32>>, budget: i32) -> i32 {
        // 1. 统计各 factor 出现次数，并记录最小单价（用于剩余预算购买"性价比最高"的物品）
        let max_factor = items.iter().map(|p| p[0]).max().unwrap() as usize;
        let min_price = items.iter().map(|p| p[1]).min().unwrap();

        let mut cnt_factor = vec![0i32; max_factor + 1];
        for p in &items {
            cnt_factor[p[0] as usize] += 1;
        }

        // 2. 每种 factor 作为一个"物品"（价值 = 它能免费带出的物品数 cnt），
        //    无限个可购买，做完全背包；背包容量为 budget。
        let budget = budget as usize;
        let mut f = vec![0i32; budget + 1];
        let mut cnt_multi = vec![0i32; max_factor + 1];
        let mut sum_price = 0usize; // 已遍历物品的价格和（用于限制 DP 上界，剪枝）

        for p in &items {
            let factor = p[0] as usize;
            let price = p[1] as usize;

            // 计算 factor 能整除的所有物品数量（只算一次）
            let cnt = if cnt_multi[factor] == 0 {
                let mut c = 0;
                let mut j = factor;
                while j <= max_factor {
                    c += cnt_factor[j];
                    j += factor;
                }
                cnt_multi[factor] = c;
                c
            } else {
                cnt_multi[factor]
            };

            // 完全背包：从 sum_price 往下更新
            sum_price = (sum_price + price).min(budget);
            let mut j = sum_price;
            while j >= price {
                f[j] = f[j].max(f[j - price] + cnt);
                j -= 1;
            }
        }

        // 3. 枚举在背包上花掉 i 元，剩余预算全买单价最小的物品
        let mut ans = 0;
        for i in 0..=budget {
            ans = ans.max(f[i] + (budget - i) as i32 / min_price);
        }
        ans
    }
}
```
