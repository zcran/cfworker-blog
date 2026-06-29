---
title: "leetcode-有序集合18"
date: 2026-06-14T10:47:41+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 设计电影租借系统

你有一个电影租借公司和 n 个电影商店。你想要实现一个电影租借系统，它支持查询、预订和返还电影的操作。同时系统还能生成一份当前被借出电影的报告。

所有电影用二维整数数组 entries 表示，其中 entries[i] = [shopi, moviei, pricei] 表示商店 shopi 有一份电影 moviei 的拷贝，租借价格为 pricei 。每个商店有 至多一份 编号为 moviei 的电影拷贝。

系统需要支持以下操作：

· Search：找到拥有指定电影且 未借出 的商店中 最便宜的 5 个 。商店需要按照 价格 升序排序，如果价格相同，则 shopi 较小 的商店排在前面。如果查询结果少于 5 个商店，则将它们全部返回。如果查询结果没有任何商店，则返回空列表。
· Rent：从指定商店借出指定电影，题目保证指定电影在指定商店 未借出 。
· Drop：在指定商店返还 之前已借出 的指定电影。
· Report：返回 最便宜的 5 部已借出电影 （可能有重复的电影 ID），将结果用二维列表 res 返回，其中 res[j] = [shopj, moviej] 表示第 j 便宜的已借出电影是从商店 shopj 借出的电影 moviej 。res 中的电影需要按 价格 升序排序；如果价格相同，则 shopj 较小 的排在前面；如果仍然相同，则 moviej 较小 的排在前面。如果当前借出的电影小于 5 部，则将它们全部返回。如果当前没有借出电影，则返回一个空的列表。

请你实现 MovieRentingSystem 类：

· MovieRentingSystem(int n, int[][] entries) 将 MovieRentingSystem 对象用 n 个商店和 entries 表示的电影列表初始化。
· List<Integer> search(int movie) 如上所述，返回 未借出 指定 movie 的商店列表。
· void rent(int shop, int movie) 从指定商店 shop 借出指定电影 movie 。
· void drop(int shop, int movie) 在指定商店 shop 返还之前借出的电影 movie 。
· List<List<Integer>> report() 如上所述，返回最便宜的 已借出 电影列表。

注意：测试数据保证 rent 操作中指定商店拥有 未借出 的指定电影，且 drop 操作指定的商店 之前已借出 指定电影。


```
use std::collections::{HashMap, BTreeSet};
use std::cmp::Ordering;

/// 电影租借系统
///
/// 支持：
/// - 搜索未借出的电影（返回最便宜的5个商店）
/// - 借出/归还电影
/// - 报告当前借出的最便宜的5部电影
struct MovieRentingSystem {
    /// 价格表: (shop, movie) -> price
    prices: HashMap<(i32, i32), i32>,

    /// 可用电影: movie -> BTreeSet<(price, shop)>
    /// 按价格升序，价格相同按商店升序
    available: HashMap<i32, BTreeSet<(i32, i32)>>,

    /// 已借出电影: BTreeSet<(price, shop, movie)>
    /// 按价格升序，价格相同按商店升序，再相同按电影升序
    rented: BTreeSet<(i32, i32, i32)>,
}

impl MovieRentingSystem {
    /// 初始化系统
    ///
    /// # 参数
    /// - `n`: 商店数量（未使用，为了API兼容）
    /// - `entries`: [shop, movie, price] 数组
    pub fn new(_n: i32, entries: Vec<Vec<i32>>) -> Self {
        let mut prices = HashMap::new();
        let mut available: HashMap<i32, BTreeSet<(i32, i32)>> = HashMap::new();

        for entry in entries {
            let (shop, movie, price) = (entry[0], entry[1], entry[2]);
            prices.insert((shop, movie), price);
            available.entry(movie).or_default().insert((price, shop));
        }

        Self {
            prices,
            available,
            rented: BTreeSet::new(),
        }
    }

    /// 搜索指定电影的可用商店
    ///
    /// # 返回
    /// 最便宜的5个商店ID列表（按价格升序，价格相同按商店升序）
    pub fn search(&self, movie: i32) -> Vec<i32> {
        self.available
            .get(&movie)
            .map(|set| {
                set.iter()
                    .take(5)
                    .map(|&(_, shop)| shop)
                    .collect()
            })
            .unwrap_or_default()
    }

    /// 借出电影
    ///
    /// # 参数
    /// - `shop`: 商店ID
    /// - `movie`: 电影ID
    ///
    /// # 保证
    /// 电影在该商店未借出
    pub fn rent(&mut self, shop: i32, movie: i32) {
        let price = self.prices[&(shop, movie)];

        // 从可用列表中移除
        if let Some(set) = self.available.get_mut(&movie) {
            set.remove(&(price, shop));
        }

        // 添加到已借出列表
        self.rented.insert((price, shop, movie));
    }

    /// 归还电影
    ///
    /// # 参数
    /// - `shop`: 商店ID
    /// - `movie`: 电影ID
    ///
    /// # 保证
    /// 电影在该商店已借出
    pub fn drop(&mut self, shop: i32, movie: i32) {
        let price = self.prices[&(shop, movie)];

        // 从已借出列表中移除
        self.rented.remove(&(price, shop, movie));

        // 添加到可用列表
        self.available.entry(movie).or_default().insert((price, shop));
    }

    /// 报告当前借出的电影
    ///
    /// # 返回
    /// 最便宜的5部已借出电影列表 [shop, movie]
    /// 按价格升序，价格相同按商店升序，再相同按电影升序
    pub fn report(&self) -> Vec<Vec<i32>> {
        self.rented
            .iter()
            .take(5)
            .map(|&(_, shop, movie)| vec![shop, movie])
            .collect()
    }
}
```
