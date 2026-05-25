---
title: "leetcode-树状树组17"
date: 2026-05-03T20:07:52+08:00
tags: ["leetcode", "树状树组"]
draft: false
---


## 统计包含每个点的矩形数目

给你一个二维整数数组 rectangles ，其中 rectangles[i] = [li, hi] 表示第 i 个矩形长为 li 高为 hi 。给你一个二维整数数组 points ，其中 points[j] = [xj, yj] 是坐标为 (xj, yj) 的一个点。

第 i 个矩形的 左下角 在 (0, 0) 处，右上角 在 (li, hi) 。

请你返回一个整数数组 count ，长度为 points.length，其中 count[j]是 包含 第 j 个点的矩形数目。

如果 0 <= xj <= li 且 0 <= yj <= hi ，那么我们说第 i 个矩形包含第 j 个点。如果一个点刚好在矩形的 边上 ，这个点也被视为被矩形包含。

```
impl Solution {
    /// 统计每个点被多少个矩形包含。
    /// 矩形由 `[width, height]` 表示，点由 `[x, y]` 表示。
    /// 点被矩形包含的条件：`width >= x` 且 `height >= y`。
    ///
    /// 算法：按高度分组存储矩形的宽度，并对每组宽度排序。
    /// 对于每个点，遍历所有可能的高度（1..=100），只考虑高度 >= 点纵坐标的组，
    /// 然后在对应组的宽度列表中二分查找第一个 >= 点横坐标的位置，累加数量。
    pub fn count_rectangles(rectangles: Vec<Vec<i32>>, points: Vec<Vec<i32>>) -> Vec<i32> {
        // 按高度分组存储宽度，高度范围 1..=100，索引 0 未使用
        let mut by_height = vec![Vec::new(); 101];
        for rect in &rectangles {
            let w = rect[0];
            let h = rect[1] as usize;
            by_height[h].push(w);
        }

        // 对每组宽度排序，便于二分查找
        for list in &mut by_height {
            list.sort_unstable();
        }

        // 处理每个点：利用函数式风格计算包含它的矩形个数
        points
            .iter()
            .map(|point| {
                let x = point[0];
                let y = point[1] as usize;

                // 遍历高度 >= y 的所有组，统计宽度 >= x 的数量
                (y..=100)
                    .filter_map(|h| {
                        let widths = &by_height[h];
                        if widths.is_empty() {
                            return None;
                        }
                        // 二分查找第一个 >= x 的位置
                        let idx = widths.partition_point(|&w| w < x);
                        Some((widths.len() - idx) as i32)
                    })
                    .sum()
            })
            .collect()
    }
}
```
