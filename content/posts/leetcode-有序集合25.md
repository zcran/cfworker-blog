---
title: "leetcode-有序集合25"
date: 2026-06-14T10:47:42+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 合并相似的物品

给你两个二维整数数组 items1 和 items2 ，表示两个物品集合。每个数组 items 有以下特质：

· items[i] = [valuei, weighti] 其中 valuei 表示第 i 件物品的 价值 ，weighti 表示第 i 件物品的 重量 。
· items 中每件物品的价值都是 唯一的 。

请你返回一个二维数组 ret，其中 ret[i] = [valuei, weighti]， weighti 是所有价值为 valuei 物品的 重量之和 。

注意：ret 应该按价值 升序 排序后返回。


```
use std::collections::BTreeMap;

impl Solution {
    /// 合并两个物品集合，相同价值的物品重量相加
    ///
    /// # 参数
    /// - `items1`: 第一个物品集合 [value, weight]
    /// - `items2`: 第二个物品集合 [value, weight]
    ///
    /// # 返回
    /// 合并后的物品集合，按价值升序排列
    ///
    /// # 时间复杂度
    /// O((n + m) log (n + m))
    ///
    /// # 空间复杂度
    /// O(n + m)
    pub fn merge_similar_items(items1: Vec<Vec<i32>>, items2: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        let mut map = BTreeMap::new();

        // 合并 items1
        for item in items1 {
            *map.entry(item[0]).or_insert(0) += item[1];
        }

        // 合并 items2
        for item in items2 {
            *map.entry(item[0]).or_insert(0) += item[1];
        }

        // 转换为结果格式（BTreeMap 已按 key 排序）
        map.into_iter()
            .map(|(value, weight)| vec![value, weight])
            .collect()
    }
}
```
