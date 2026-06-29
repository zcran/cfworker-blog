---
title: "leetcode-有序集合17"
date: 2026-06-14T10:47:41+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 最近的房间

一个酒店里有 n 个房间，这些房间用二维整数数组 rooms 表示，其中 rooms[i] = [roomIdi, sizei] 表示有一个房间号为 roomIdi 的房间且它的面积为 sizei 。每一个房间号 roomIdi 保证是 独一无二 的。

同时给你 k 个查询，用二维数组 queries 表示，其中 queries[j] = [preferredj, minSizej] 。第 j 个查询的答案是满足如下条件的房间 id ：

· 房间的面积 至少 为 minSizej ，且
· abs(id - preferredj) 的值 最小 ，其中 abs(x) 是 x 的绝对值。

如果差的绝对值有 相等 的，选择 最小 的 id 。如果 没有满足条件的房间 ，答案为 -1 。

请你返回长度为 k 的数组 answer ，其中 answer[j] 为第 j 个查询的结果。


```
use std::collections::BTreeSet;

impl Solution {
    /// 为每个查询找到最合适的房间
    ///
    /// # 思路
    /// 离线处理：按面积降序排序房间和查询，维护可用房间集合
    ///
    /// # 时间复杂度
    /// O((n+k) log n)
    ///
    /// # 空间复杂度
    /// O(n+k)
    pub fn closest_room(rooms: Vec<Vec<i32>>, queries: Vec<Vec<i32>>) -> Vec<i32> {
        // 1. 按面积降序排序房间
        let mut sorted_rooms = rooms;
        sorted_rooms.sort_by(|a, b| b[1].cmp(&a[1]));

        // 2. 为查询添加索引并按面积降序排序
        let mut sorted_queries: Vec<(i32, i32, usize)> = queries
            .iter()
            .enumerate()
            .map(|(i, q)| (q[1], q[0], i))  // (minSize, preferred, index)
            .collect();
        sorted_queries.sort_by(|a, b| b.0.cmp(&a.0));

        // 3. 维护可用房间集合
        let mut available: BTreeSet<i32> = BTreeSet::new();
        let mut ans = vec![-1; queries.len()];
        let mut room_idx = 0;

        for &(min_size, preferred, idx) in &sorted_queries {
            // 添加所有面积 >= min_size 的房间
            while room_idx < sorted_rooms.len() && sorted_rooms[room_idx][1] >= min_size {
                available.insert(sorted_rooms[room_idx][0]);
                room_idx += 1;
            }

            if available.is_empty() {
                continue;
            }

            // 查找最接近 preferred 的房间
            let mut best_dist = i32::MAX;
            let mut best_id = -1;

            // 候选1: >= preferred 的最小房间
            if let Some(&room_id) = available.range(preferred..).next() {
                let dist = room_id - preferred;
                best_dist = dist;
                best_id = room_id;
            }

            // 候选2: < preferred 的最大房间
            if let Some(&room_id) = available.range(..preferred).next_back() {
                let dist = preferred - room_id;
                if dist < best_dist || (dist == best_dist && room_id < best_id) {
                    best_id = room_id;
                }
            }

            ans[idx] = best_id;
        }

        ans
    }
}
```
