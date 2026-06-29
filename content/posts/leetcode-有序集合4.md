---
title: "leetcode-有序集合4"
date: 2026-06-14T10:47:41+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 掉落的方块

在二维平面上的 x 轴上，放置着一些方块。

给你一个二维整数数组 positions ，其中 positions[i] = [lefti, sideLengthi] 表示：第 i 个方块边长为 sideLengthi ，其左侧边与 x 轴上坐标点 lefti 对齐。

每个方块都从一个比目前所有的落地方块更高的高度掉落而下。方块沿 y 轴负方向下落，直到着陆到 另一个正方形的顶边 或者是 x 轴上 。一个方块仅仅是擦过另一个方块的左侧边或右侧边不算着陆。一旦着陆，它就会固定在原地，无法移动。

在每个方块掉落后，你必须记录目前所有已经落稳的 方块堆叠的最高高度 。

返回一个整数数组 ans ，其中 ans[i] 表示在第 i 块方块掉落后堆叠的最高高度。


```
use std::collections::BTreeMap;

impl Solution {
    pub fn falling_squares(positions: Vec<Vec<i32>>) -> Vec<i32> {
        // 区间映射：key = 左端点，value = (右端点, 高度)
        let mut intervals: BTreeMap<i32, (i32, i32)> = BTreeMap::new();
        let mut result = Vec::with_capacity(positions.len());
        let mut global_max = 0;

        for pos in positions {
            let left = pos[0];
            let size = pos[1];
            let right = left + size - 1;

            // 步骤1：查找所有与 [left, right] 重叠的区间
            let mut max_height = 0;
            let mut to_process = Vec::new();

            // 收集所有可能重叠的区间
            for (&l, &(r, h)) in intervals.range(..=right) {
                if r < left {
                    continue; // 不重叠，跳过
                }
                if l > right {
                    break; // 后续区间都在右侧，不再重叠
                }
                max_height = max_height.max(h);
                to_process.push((l, r, h));
            }

            // 步骤2：删除所有重叠的区间
            for &(l, _, _) in &to_process {
                intervals.remove(&l);
            }

            // 步骤3：重新插入未被覆盖的部分和新区间
            // 核心：保留所有未与 [left, right] 重叠的部分
            for (l, r, h) in to_process {
                // 左半部分：[l, left-1] 未被覆盖
                if l < left {
                    intervals.insert(l, (left - 1, h));
                }
                // 右半部分：[right+1, r] 未被覆盖
                if r > right {
                    intervals.insert(right + 1, (r, h));
                }
                // 中间部分 [max(l,left), min(r,right)] 被新区块覆盖
                // 将被新区块的高度替代
            }

            // 插入新区块（高度 = max_height + size）
            let new_height = max_height + size;
            intervals.insert(left, (right, new_height));

            // 步骤4：更新全局最大高度
            global_max = global_max.max(new_height);
            result.push(global_max);
        }

        result
    }
}
```
