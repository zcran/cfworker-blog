---
title: "Leetcode 水塘抽样1"
date: 2023-12-19T15:21:14+08:00
tags: ["leetcode", "水塘抽样"]
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
use rand::{thread_rng, Rng};

struct Solution {
    rects: Vec<Vec<i32>>,
    arr: Vec<i32>,
}

impl Solution {
    fn new(rects: Vec<Vec<i32>>) -> Self {
        let arr = rects
            .iter()
            .scan(0, |v, rect| {
                *v += (rect[2] - rect[0] + 1) * (rect[3] - rect[1] + 1);
                Some(*v)
            })
            .collect();

        Self { rects, arr }
    }

    fn pick(&self) -> Vec<i32> {
        let mut rng = thread_rng();
        let v: i32 = rng.gen_range(1, self.arr.last().unwrap() + 1);
        let index = self.arr.binary_search(&v).map_or_else(|e| e, |v| v);
        let offset = if index == 0 { 1 } else { self.arr[index - 1] + 1 };
        let width = self.rects[index][2] - self.rects[index][0] + 1;
        let v = v - offset;

        vec![
            v % width + self.rects[index][0],
            v / width + self.rects[index][1],
        ]
    }
}
```