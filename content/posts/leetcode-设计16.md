---
title: "leetcode-设计16"
date: 2026-07-14T10:41:25+08:00
tags: ["leetcode", "设计"]
draft: false
---


## 每隔 n 个顾客打折

超市里正在举行打折活动，每隔 n 个顾客会得到 discount 的折扣。

超市里有一些商品，第 i 种商品为 products[i] 且每件单品的价格为 prices[i] 。

结账系统会统计顾客的数目，每隔 n 个顾客结账时，该顾客的账单都会打折，折扣为 discount （也就是如果原本账单为 x ，那么实际金额会变成 x - (discount * x) / 100 ），然后系统会重新开始计数。

顾客会购买一些商品， product[i] 是顾客购买的第 i 种商品， amount[i] 是对应的购买该种商品的数目。

请你实现 Cashier 类：

Cashier(int n, int discount, int[] products, int[] prices) 初始化实例对象，参数分别为打折频率 n ，折扣大小 discount ，超市里的商品列表 products 和它们的价格 prices 。

double getBill(int[] product, int[] amount) 返回账单的实际金额（如果有打折，请返回打折后的结果）。返回结果与标准答案误差在 10^-5 以内都视为正确结果。


```
use std::collections::HashMap;

/// 超市收银系统，支持周期性打折
struct Cashier {
    /// 打折频率：每 n 个顾客打折一次
    frequency: i32,
    /// 当前顾客计数（从 1 开始）
    customer_count: i32,
    /// 折扣百分比（如 20 表示打八折）
    discount_percent: i32,
    /// 商品 ID -> 价格 的映射
    prices: HashMap<i32, i32>,
}

impl Cashier {
    /// 初始化收银系统
    ///
    /// # 参数
    /// - `n`: 打折频率，每 n 个顾客享受一次折扣
    /// - `discount`: 折扣百分比（0-100）
    /// - `products`: 商品 ID 列表
    /// - `prices`: 对应商品的价格列表
    pub fn new(n: i32, discount: i32, products: Vec<i32>, prices: Vec<i32>) -> Self {
        Self {
            frequency: n,
            customer_count: 0,
            discount_percent: discount,
            prices: products.into_iter().zip(prices).collect(),
        }
    }

    /// 计算顾客购物账单
    ///
    /// # 参数
    /// - `product`: 购买的商品 ID 列表
    /// - `amount`: 对应商品的数量列表
    ///
    /// # 返回
    /// 实际应付金额（如果该顾客享受折扣，则应用折扣）
    ///
    /// # 复杂度
    /// 时间复杂度: O(k)，k 为购买的商品种类数
    /// 空间复杂度: O(1)
    pub fn get_bill(&mut self, product: Vec<i32>, amount: Vec<i32>) -> f64 {
        // 顾客计数加 1
        self.customer_count += 1;

        // 计算原始总价
        let total: i32 = product
            .iter()
            .zip(&amount)
            .map(|(id, &qty)| self.prices[id] * qty)
            .sum();

        // 判断是否享受折扣
        if self.customer_count % self.frequency == 0 {
            // 应用折扣：总价 * (1 - discount/100)
            total as f64 * (1.0 - self.discount_percent as f64 / 100.0)
        } else {
            total as f64
        }
    }
}
```
