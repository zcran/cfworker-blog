---
title: "Leetcode 拒绝采样2"
date: 2023-06-12T10:56:46+08:00
tags: ["leetcode", "拒绝采样"]
draft: false
---

## 在圆内随机生成点

给定圆的半径和圆心的位置，实现函数 randPoint ，在圆中产生均匀随机点。

实现 Solution 类:

Solution(double radius, double x_center, double y_center) 用圆的半径 radius 和圆心的位置 (x_center, y_center) 初始化对象
randPoint() 返回圆内的一个随机点。圆周上的一点被认为在圆内。答案作为数组返回 [x, y] 。

```
struct Solution {
    radius: f64,
    x_center: f64,
    y_center: f64,
}


/**
 * `&self` means the method takes an immutable reference.
 * If you need a mutable reference, change it to `&mut self` instead.
 */
impl Solution {

    fn new(radius: f64, x_center: f64, y_center: f64) -> Self {
         Solution { radius, x_center, y_center } 
    }
    
    fn rand_point(&self) -> Vec<f64> {
        use rand::{Rng, thread_rng};
        use std::f64::consts::PI;
        let mut rng = thread_rng();
        let (len, deg) = (rng.gen::<f64>().sqrt() * self.radius, rng.gen::<f64>() * PI * 2.0);
        vec![self.x_center + len * deg.cos(), self.y_center + len * deg.sin()]

    }
}

/**
 * Your Solution object will be instantiated and called as such:
 * let obj = Solution::new(radius, x_center, y_center);
 * let ret_1: Vec<f64> = obj.rand_point();
 */
```