---
title: "leetcode-数据流10"
date: 2026-04-06T20:57:54+08:00
tags: ["leetcode", "数据流"]
draft: false
---


## 检测正方形

给你一个在 X-Y 平面上的点构成的数据流。设计一个满足下述要求的算法：

添加 一个在数据流中的新点到某个数据结构中。可以添加 重复 的点，并会视作不同的点进行处理。
给你一个查询点，请你从数据结构中选出三个点，使这三个点和查询点一同构成一个 面积为正 的 轴对齐正方形 ，统计 满足该要求的方案数目。
轴对齐正方形 是一个正方形，除四条边长度相同外，还满足每条边都与 x-轴 或 y-轴 平行或垂直。

实现 DetectSquares 类：

DetectSquares() 使用空数据结构初始化对象
void add(int[] point) 向数据结构添加一个新的点 point = [x, y]
int count(int[] point) 统计按上述方式与点 point = [x, y] 共同构造 轴对齐正方形 的方案数。


```
use std::collections::HashMap;
struct DetectSquares {
    y: HashMap<i32, HashMap<i32, i32>>,
}

impl DetectSquares {
    fn new() -> Self {
        DetectSquares { y: HashMap::new() }
    }

    fn add(&mut self, point: Vec<i32>) {
        let (x, y) = (point[0], point[1]);        // 接收一个点坐标 [x, y]。解构赋值，分别取出 x 和 y
        *self
            .y
            .entry(y)       // 获取 y 坐标对应的条目
            .or_insert(HashMap::new())      // 如果该 y 不存在，插入一个新的空 HashMap；返回内层 HashMap 的可变引用
            .entry(x)       // 在这个内层 HashMap 中获取 x 坐标对应的条目
            .or_insert(0) += 1;       // 如果该 x 不存在，插入 0；返回该值的可变引用
            // *... += 1        解引用该值并加 1
    }

    fn count(&self, point: Vec<i32>) -> i32 {
        let mut ans = 0;
        let (x, y) = (point[0], point[1]);
        if let Some(cur_xs) = self.y.get(&y) {      // 从 self.y 中获取与查询点 相同 y 的所有点
            for (&x1, &n1) in cur_xs.iter() {     // 遍历所有与查询点相同 y 的点
                if x == x1 {
                    continue;
                }
                let d = (x - x1).abs();       // 计算查询点与候选点的水平距离 d 。这个距离将作为正方形的边长
                for next_y in [y + d, y - d] {      // 正方形需要向 上 (y + d) 和 下 (y - d) 两个方向寻找  。遍历这两个可能的 y 坐标
                    if let Some(other_xs) = self.y.get(&next_y) {       // 检查是否存在该 y 坐标的点集
                        let (n2, n3) = (other_xs.get(&x), other_xs.get(&x1));
                        if n2.is_none() || n3.is_none() {
                            continue;       // 如果缺少任何一个点，无法形成正方形
                        }
                        let (&n2, &n3) = (n2.unwrap(), n3.unwrap());
                        ans += n1 * n2 * n3;    // 否则，三个点的数量相乘得到该组合的正方形数量
                    }
                }
            }
        } else {
            return 0;
        }
        ans
    }
}


```


假设查询点 P = (x, y)，找到一个候选点 A = (x1, y)：

text

复制

下载
    (x, y+d) ───── (x1, y+d)
       ↑              ↑
       | d            | d
       |              |
    P(x, y) ──────→ A(x1, y)
         ← d →
点 P 和 A 构成正方形的底边
需要向上（y+d）或向下（y-d）找到另外两个点
形成正方形需要四个顶点都存在
可能的正方形：

向上：(x, y)、(x1, y)、(x, y+d)、(x1, y+d)
向下：(x, y)、(x1, y)、(x, y-d)、(x1, y-d)
