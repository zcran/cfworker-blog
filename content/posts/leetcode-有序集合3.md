---
title: "leetcode-有序集合3"
date: 2026-06-14T10:47:41+08:00
tags: ["leetcode", "有序集合"]
draft: false
---


## 非重叠矩形中的随机点

给定一个由非重叠的轴对齐矩形的数组 rects ，其中 rects[i] = [ai, bi, xi, yi] 表示 (ai, bi) 是第 i 个矩形的左下角点，(xi, yi) 是第 i 个矩形的右上角点。设计一个算法来随机挑选一个被某一矩形覆盖的整数点。矩形周长上的点也算做是被矩形覆盖。所有满足要求的点必须等概率被返回。

在给定的矩形覆盖的空间内的任何整数点都有可能被返回。

请注意 ，整数点是具有整数坐标的点。

实现 Solution 类:

Solution(int[][] rects) 用给定的矩形数组 rects 初始化对象。

int[] pick() 返回一个随机的整数点 [u, v] 在给定的矩形所覆盖的空间内。


```
use rand::Rng;

/// 随机矩形点生成器
///
/// 算法思路：
/// 1. 按矩形面积加权随机选择一个矩形
/// 2. 在选中的矩形内均匀随机生成整数点
///
/// # 复杂度
/// - 初始化: O(n)，计算面积前缀和
/// - pick: O(log n)，二分查找选中的矩形
struct Solution {
    rects: Vec<Vec<i32>>,      // 原始矩形数据
    prefix_areas: Vec<i32>,    // 面积前缀和（用于加权随机选择）
    total_area: i32,           // 总面积
}

impl Solution {
    /// 初始化 Solution
    ///
    /// # 参数
    /// - `rects`: 矩形数组，每个矩形为 [x1, y1, x2, y2]
    pub fn new(rects: Vec<Vec<i32>>) -> Self {
        let mut prefix_areas = Vec::with_capacity(rects.len());
        let mut total_area = 0;

        // 计算每个矩形的面积并构建前缀和
        for rect in &rects {
            let width = rect[2] - rect[0] + 1;   // 包含边界上的点
            let height = rect[3] - rect[1] + 1;
            total_area += width * height;
            prefix_areas.push(total_area);
        }

        Self {
            rects,
            prefix_areas,
            total_area,
        }
    }

    /// 随机选取一个被矩形覆盖的整数点
    ///
    /// # 返回
    /// - `Vec<i32>`: [x, y] 坐标
    pub fn pick(&mut self) -> Vec<i32> {
        // 1. 按面积加权随机选择一个矩形
        let rand_val = rand::thread_rng().gen_range(1..=self.total_area);
        let idx = self.prefix_areas.binary_search(&rand_val).unwrap_or_else(|p| p);

        // 2. 在选中的矩形内随机生成点
        let rect = &self.rects[idx];
        let x = rand::thread_rng().gen_range(rect[0]..=rect[2]);
        let y = rand::thread_rng().gen_range(rect[1]..=rect[3]);

        vec![x, y]
    }
}
```
