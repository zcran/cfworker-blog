---
title: "leetcode-几何5"
date: 2026-05-11T20:20:55+08:00
tags: ["leetcode", "几何"]
draft: false
---


## 在圆内随机生成点

给定圆的半径和圆心的位置，实现函数 randPoint ，在圆中产生均匀随机点。

实现 Solution 类:

· Solution(double radius, double x_center, double y_center) 用圆的半径 radius 和圆心的位置 (x_center, y_center) 初始化对象

· randPoint() 返回圆内的一个随机点。圆周上的一点被认为在圆内。答案作为数组返回 [x, y] 。

```
use rand::random;
use std::f64::consts::TAU;

struct Solution {
    radius: f64,
    x_center: f64,
    y_center: f64,
}

impl Solution {
    pub fn new(radius: f64, x_center: f64, y_center: f64) -> Self {
        Solution { radius, x_center, y_center }
    }

    pub fn rand_point(&self) -> Vec<f64> {
        std::iter::repeat_with(|| (random::<f64>(), random::<f64>()))
            .map(|(u, v)| {
                let r = u.sqrt() * self.radius;
                let theta = v * TAU;
                let (sin_theta, cos_theta) = theta.sin_cos();
                vec![
                    self.x_center + r * cos_theta,
                    self.y_center + r * sin_theta,
                ]
            })
            .next()
            .unwrap()
    }
}
```
