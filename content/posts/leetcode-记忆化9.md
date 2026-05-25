---
title: "leetcode-记忆化9"
date: 2026-05-06T20:22:33+08:00
tags: ["leetcode", "记忆化"]
draft: false
---



## 大礼包

在 LeetCode 商店中， 有 n 件在售的物品。每件物品都有对应的价格。然而，也有一些大礼包，每个大礼包以优惠的价格捆绑销售一组物品。

给你一个整数数组 price 表示物品价格，其中 price[i] 是第 i 件物品的价格。另有一个整数数组 needs 表示购物清单，其中 needs[i] 是需要购买第 i 件物品的数量。

还有一个数组 special 表示大礼包，special[i] 的长度为 n + 1 ，其中 special[i][j] 表示第 i 个大礼包中内含第 j 件物品的数量，且 special[i][n] （也就是数组中的最后一个整数）为第 i 个大礼包的价格。

返回 确切 满足购物清单所需花费的最低价格，你可以充分利用大礼包的优惠活动。你不能购买超出购物清单指定数量的物品，即使那样会降低整体价格。任意大礼包可无限次购买。

```
impl Solution {
    /// 购物优惠问题 (LeetCode 638)
    ///
    /// 给定每种物品的单价 `price`，一些优惠套餐 `special`（每个套餐最后一项为总价），
    /// 以及当前购物清单 `needs`（每种物品所需数量）。
    /// 返回满足所有需求的最小花费，可以使用任意数量的优惠套餐，也可以单独购买。
    pub fn shopping_offers(price: Vec<i32>, special: Vec<Vec<i32>>, needs: Vec<i32>) -> i32 {
        use std::collections::HashMap;

        // ---------- 1. 过滤无效套餐 ----------
        // 只保留那些套餐总价低于单独购买该套餐内物品的总价的优惠
        let valid_special: Vec<Vec<i32>> = special
            .into_iter()
            .filter(|offer| {
                let offer_price = *offer.last().unwrap();          // 套餐价格
                let original_price: i32 = price
                    .iter()
                    .zip(offer.iter())
                    .map(|(&unit_price, &qty)| unit_price * qty)
                    .sum();
                offer_price < original_price
            })
            .collect();

        // 记忆化递归：记录每个需求状态对应的最小花费
        let mut memo = HashMap::new();

        /// 深度优先搜索 + 记忆化
        ///
        /// # 参数
        /// - `memo`: 记忆化缓存
        /// - `current_needs`: 当前还需要的每种物品数量
        /// - `price`: 单价列表（只读）
        /// - `special`: 优惠套餐列表（只读）
        fn dfs(
            memo: &mut HashMap<Vec<i32>, i32>,
            current_needs: &[i32],
            price: &[i32],
            special: &[Vec<i32>],
        ) -> i32 {
            // 如果当前状态未计算过
            if !memo.contains_key(current_needs) {
                // 基准方案：不采用任何套餐，全部按单价购买
                let mut min_cost: i32 = current_needs
                    .iter()
                    .zip(price.iter())
                    .map(|(&need, &unit_price)| need * unit_price)
                    .sum();

                // 尝试每一个优惠套餐
                let mut next_needs = Vec::with_capacity(current_needs.len());
                for offer in special {
                    let offer_price = *offer.last().unwrap();
                    let mut valid = true;

                    // 检查套餐是否不超过当前需求（所有物品数量都满足）
                    for i in 0..current_needs.len() {
                        if offer[i] > current_needs[i] {
                            valid = false;
                            break;
                        }
                    }

                    if valid {
                        // 构造使用该套餐后的新需求
                        next_needs.clear();
                        for i in 0..current_needs.len() {
                            next_needs.push(current_needs[i] - offer[i]);
                        }
                        // 递归计算剩余需求的最小花费，并加上套餐价格
                        let cost_with_offer = offer_price + dfs(memo, &next_needs, price, special);
                        if cost_with_offer < min_cost {
                            min_cost = cost_with_offer;
                        }
                    }
                }

                // 存入缓存
                memo.insert(current_needs.to_vec(), min_cost);
            }

            // 返回缓存结果
            *memo.get(current_needs).unwrap()
        }

        dfs(&mut memo, &needs, &price, &valid_special)
    }
}
```
