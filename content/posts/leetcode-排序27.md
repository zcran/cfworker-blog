---
title: "leetcode-排序27"
date: 2026-10-05T00:35:01+08:00
tags: ["leetcode", "排序"]
draft: false
---


## 餐厅过滤器

给你一个餐馆信息数组 restaurants，其中  restaurants[i] = [idi, ratingi, veganFriendlyi, pricei, distancei]。你必须使用以下三个过滤器来过滤这些餐馆信息。

其中素食者友好过滤器 veganFriendly 的值可以为 true 或者 false，如果为 true 就意味着你应该只包括 veganFriendlyi 为 true 的餐馆，为 false 则意味着可以包括任何餐馆。此外，我们还有最大价格 maxPrice 和最大距离 maxDistance 两个过滤器，它们分别考虑餐厅的价格因素和距离因素的最大值。

过滤后返回餐馆的 id，按照 rating 从高到低排序。如果 rating 相同，那么按 id 从高到低排序。简单起见， veganFriendlyi 和 veganFriendly 为 true 时取值为 1，为 false 时，取值为 0 。




```
impl Solution {
    /// 过滤餐馆并按 rating 降序、id 降序返回 id 列表
    ///
    /// 过滤条件：
    ///   - vegan_friendly == 1 时只保留 veganFriendly == 1 的餐馆；
    ///   - price ≤ max_price；
    ///   - distance ≤ max_distance。
    /// 思路：先收集通过过滤的餐馆引用，再按 (rating, id) 降序排序，最后取 id。
    /// 时间 O(n log n)，额外空间 O(n)。
    pub fn filter_restaurants(
        restaurants: Vec<Vec<i32>>,
        vegan_friendly: i32,
        max_price: i32,
        max_distance: i32,
    ) -> Vec<i32> {
        // 收集满足条件的餐馆（存引用，避免 clone）
        let mut filtered: Vec<&Vec<i32>> = restaurants
            .iter()
            .filter(|r| {
                // vegan_friendly == 0 时不限制；== 1 时要求餐厅也友好
                (vegan_friendly == 0 || r[2] == 1)
                    && r[3] <= max_price
                    && r[4] <= max_distance
            })
            .collect();

        // rating 降序；rating 相同时 id 降序
        filtered.sort_unstable_by(|a, b| b[1].cmp(&a[1]).then_with(|| b[0].cmp(&a[0])));

        filtered.iter().map(|r| r[0]).collect()
    }
}
```
