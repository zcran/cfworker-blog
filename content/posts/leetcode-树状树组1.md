---
title: "leetcode-树状树组1"
date: 2026-05-03T20:07:52+08:00
tags: ["leetcode", "树状树组"]
draft: false
---


## 天际线问题

城市的 天际线 是从远处观看该城市中所有建筑物形成的轮廓的外部轮廓。给你所有建筑物的位置和高度，请返回 由这些建筑物形成的 天际线 。

每个建筑物的几何信息由数组 buildings 表示，其中三元组 buildings[i] = [lefti, righti, heighti] 表示：

lefti 是第 i 座建筑物左边缘的 x 坐标。
righti 是第 i 座建筑物右边缘的 x 坐标。
heighti 是第 i 座建筑物的高度。
你可以假设所有的建筑都是完美的长方形，在高度为 0 的绝对平坦的表面上。

天际线 应该表示为由 “关键点” 组成的列表，格式 [[x1,y1],[x2,y2],...] ，并按 x 坐标 进行 排序 。关键点是水平线段的左端点。列表中最后一个点是最右侧建筑物的终点，y 坐标始终为 0 ，仅用于标记天际线的终点。此外，任何两个相邻建筑物之间的地面都应被视为天际线轮廓的一部分。

注意：输出天际线中不得有连续的相同高度的水平线。例如 [...[2 3], [4 5], [7 5], [11 5], [12 7]...] 是不正确的答案；三条高度为 5 的线应该在最终输出中合并为一个：[...[2 3], [4 5], [12 7], ...]

```
use std::collections::BinaryHeap;

impl Solution {
    /// 计算天际线轮廓（扫描线 + 最大堆）
    /// 时间复杂度 O(n log n)，空间复杂度 O(n)
    pub fn get_skyline(buildings: Vec<Vec<i32>>) -> Vec<Vec<i32>> {
        // 1. 收集所有左右端点，排序并去重
        let mut scan_positions: Vec<i32> = buildings
            .iter()
            .flat_map(|b| [b[0], b[1]])
            .collect();
        scan_positions.sort_unstable();
        scan_positions.dedup();

        // 2. fold 扫描线，累加器: (当前建筑物索引, 最大堆, 结果集)
        let (_, _, result) = scan_positions
            .iter()
            .fold(
                (
                    0_usize,                               // idx
                    BinaryHeap::<(i32, i32)>::new(),       // 堆: (高度, 右端点)
                    Vec::<Vec<i32>>::new(),                // 结果集
                ),
                |(mut idx, mut heap, mut result), &x| {
                    // 2.1 所有左端点 <= x 的建筑入堆
                    while idx < buildings.len() && buildings[idx][0] <= x {
                        heap.push((buildings[idx][2], buildings[idx][1]));
                        idx += 1;
                    }

                    // 2.2 移除右端点 <= x 的过期建筑
                    while let Some(&(_, right)) = heap.peek() {
                        if right <= x {
                            heap.pop();
                        } else {
                            break;
                        }
                    }

                    // 2.3 当前最高高度（无建筑时为 0）
                    let current_height = heap.peek().map(|&(h, _)| h).unwrap_or(0);

                    // 2.4 高度变化时记录关键点
                    if result.is_empty() || result.last().unwrap()[1] != current_height {
                        result.push(vec![x, current_height]);
                    }

                    (idx, heap, result)
                },
            );

        result
    }
}
```
