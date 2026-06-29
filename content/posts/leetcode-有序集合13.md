---
title: "leetcode-有序集合13"
date: 2026-06-14T10:47:41+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 点菜展示表

给你一个数组 orders，表示客户在餐厅中完成的订单，确切地说， orders[i]=[customerNamei,tableNumberi,foodItemi] ，其中 customerNamei 是客户的姓名，tableNumberi 是客户所在餐桌的桌号，而 foodItemi 是客户点的餐品名称。

请你返回该餐厅的 点菜展示表 。在这张表中，表中第一行为标题，其第一列为餐桌桌号 “Table” ，后面每一列都是按字母顺序排列的餐品名称。接下来每一行中的项则表示每张餐桌订购的相应餐品数量，第一列应当填对应的桌号，后面依次填写下单的餐品数量。

注意：客户姓名不是点菜展示表的一部分。此外，表中的数据行应该按餐桌桌号升序排列。


```
use std::collections::{BTreeMap, HashMap};

impl Solution {
    /// 生成餐厅的点菜展示表
    ///
    /// # 参数
    /// - `orders`: 订单数组，每个订单为 [客户名, 桌号, 餐品名]
    ///
    /// # 返回
    /// 点菜展示表，第一行为标题，后续行为各桌的订单统计
    ///
    /// # 时间复杂度
    /// O(n log m + t log t)，其中 n 是订单数，m 是餐品数，t 是桌数
    ///
    /// # 空间复杂度
    /// O(n + m + t)
    pub fn display_table(orders: Vec<Vec<String>>) -> Vec<Vec<String>> {
        // 1. 收集所有餐品并按字典序排序（BTreeMap自动排序）
        let mut foods: BTreeMap<String, usize> = BTreeMap::new();
        for order in &orders {
            foods.entry(order[2].clone()).or_insert(0);
        }

        // 2. 为每个餐品分配索引
        for (idx, (_, count)) in foods.iter_mut().enumerate() {
            *count = idx;
        }

        let food_count = foods.len();

        // 3. 统计各桌的订单数量
        let mut tables: HashMap<i32, Vec<usize>> = HashMap::new();
        for order in &orders {
            let table_num = order[1].parse::<i32>().unwrap();
            let food_idx = foods[&order[2]];

            tables
                .entry(table_num)
                .or_insert_with(|| vec![0; food_count])[food_idx] += 1;
        }

        // 4. 构建结果表格
        let mut result = Vec::with_capacity(tables.len() + 1);

        // 4.1 构建标题行
        let mut header = Vec::with_capacity(food_count + 1);
        header.push("Table".to_string());
        header.extend(foods.keys().cloned());
        result.push(header);

        // 4.2 构建数据行（按桌号升序）
        let sorted_tables: BTreeMap<_, _> = tables.into_iter().collect();
        for (table_num, counts) in sorted_tables {
            let mut row = Vec::with_capacity(counts.len() + 1);
            row.push(table_num.to_string());
            row.extend(counts.iter().map(|&c| c.to_string()));
            result.push(row);
        }

        result
    }
}
```
