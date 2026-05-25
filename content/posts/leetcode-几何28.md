---
title: "leetcode-几何28"
date: 2026-05-11T20:20:56+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 最小化曼哈顿距离

给你一个下标从 0 开始的数组 points ，它表示二维平面上一些点的整数坐标，其中 points[i] = [xi, yi] 。

两点之间的距离定义为它们的曼哈顿距离。

请你恰好移除一个点，返回移除后任意两点之间的 最大 距离可能的 最小 值。


```
use std::collections::BTreeMap;

impl Solution {
    /// 计算删除一个点后，剩余点之间曼哈顿距离的最大值的最小可能值
    ///
    /// 通过 45° 旋转将曼哈顿距离转换为切比雪夫距离：
    /// 对于点 (x, y)，定义 u = x - y, v = x + y。
    /// 则两点曼哈顿距离 = max(|u1-u2|, |v1-v2|)。
    /// 因此，点集的曼哈顿直径 = max( max_u - min_u, max_v - min_v )。
    /// 本算法枚举删除每个点，快速计算剩余点集的直径并取最小值。
    pub fn minimum_distance(points: Vec<Vec<i32>>) -> i32 {
        // 统计每个 u 和 v 值的出现次数（用于快速判断删除后是否为空）
        let mut count_u = BTreeMap::new();
        let mut count_v = BTreeMap::new();

        // 第一遍扫描：建立频率表
        for p in &points {
            let u = p[0] - p[1];
            let v = p[0] + p[1];
            *count_u.entry(u).or_insert(0) += 1;
            *count_v.entry(v).or_insert(0) += 1;
        }

        // 辅助函数：临时移除一个点（减少计数，若归零则删除键）
        let remove_point = |map: &mut BTreeMap<i32, i32>, key: i32| {
            let entry = map.entry(key).or_insert(0);
            *entry -= 1;
            if *entry == 0 {
                map.remove(&key);
            }
        };

        // 辅助函数：恢复一个点（增加计数）
        let restore_point = |map: &mut BTreeMap<i32, i32>, key: i32| {
            *map.entry(key).or_insert(0) += 1;
        };

        let mut ans = i32::MAX;

        for p in &points {
            let u = p[0] - p[1];
            let v = p[0] + p[1];

            // 临时移除当前点
            remove_point(&mut count_u, u);
            remove_point(&mut count_v, v);

            // 若两个映射都非空，计算当前直径（切比雪夫距离）
            if !count_u.is_empty() && !count_v.is_empty() {
                // 直接获取最小和最大键，避免创建迭代器
                let min_u = *count_u.first_key_value().unwrap().0;
                let max_u = *count_u.last_key_value().unwrap().0;
                let min_v = *count_v.first_key_value().unwrap().0;
                let max_v = *count_v.last_key_value().unwrap().0;

                let diameter = (max_u - min_u).max(max_v - min_v);
                ans = ans.min(diameter);
            }

            // 恢复当前点
            restore_point(&mut count_u, u);
            restore_point(&mut count_v, v);
        }

        ans
    }
}
```
