---
title: "Leetcode 随机化12"
date: 2024-12-21T15:03:43+08:00
tags: ["leetcode", "随机化"]
draft: false
---
## 服务中心的最佳位置

一家快递公司希望在新城市建立新的服务中心。公司统计了该城市所有客户在二维地图上的坐标，并希望能够以此为依据为新的服务中心选址：使服务中心 到所有客户的欧几里得距离的总和最小 。

给你一个数组 positions ，其中 positions[i] = [xi, yi] 表示第 i 个客户在二维地图上的位置，返回到所有客户的 欧几里得距离的最小总和 。

换句话说，请你为服务中心选址，该位置的坐标 [xcentre, ycentre] 需要使下面的公式取到最小值：
[Alt text](../../resources/_gen/images/formula3.jpg)

```
use rand::seq::SliceRandom;
use rand::{thread_rng, Rng};

impl Solution {
    pub fn get_min_dist_sum(positions: Vec<Vec<i32>>) -> f64 {
        let eps = 1e-7;
        let mut alpha = 1.0;
        let decay = 1e-3;
        
        let n = positions.len();
        let batch_size = n;
        
        // 计算初始中心点
        let mut x = positions.iter().map(|pos| pos[0] as f64).sum::<f64>() / n as f64;
        let mut y = positions.iter().map(|pos| pos[1] as f64).sum::<f64>() / n as f64;
        
        // 计算距离和的闭包
        let get_dist = |xc: f64, yc: f64| {
            positions.iter()
                .map(|pos| {
                    let dx = pos[0] as f64 - xc;
                    let dy = pos[1] as f64 - yc;
                    (dx * dx + dy * dy).sqrt()
                })
                .sum::<f64>()
        };
        
        let mut rng = thread_rng();
        let mut shuffled_positions = positions.clone();
        
        loop {
            // 随机打乱位置
            shuffled_positions.shuffle(&mut rng);
            let x_prev = x;
            let y_prev = y;
            
            for i in (0..n).step_by(batch_size) {
                let j = std::cmp::min(i + batch_size, n);
                let mut dx = 0.0;
                let mut dy = 0.0;
                
                for k in i..j {
                    let pos = &shuffled_positions[k];
                    let x_diff = x - pos[0] as f64;
                    let y_diff = y - pos[1] as f64;
                    let denominator = (x_diff * x_diff + y_diff * y_diff).sqrt() + eps;
                    dx += x_diff / denominator;
                    dy += y_diff / denominator;
                }
                
                x -= alpha * dx;
                y -= alpha * dy;
                
                // 学习率衰减
                alpha *= (1.0 - decay);
            }
            
            // 检查是否收敛
            if ((x - x_prev).powi(2) + (y - y_prev).powi(2)).sqrt() < eps {
                break;
            }
        }
        
        get_dist(x, y)
    }
}
```

「梯度下降法」是机器学习中常用的一种求解局部最小值的算法。对于给定的点 (x,y)，它的梯度方向是函数值上升最快的方向，因此梯度反向就是函数值下降最快的方向。本题中需要优化的函数为：
[Alt text](../../resources/_gen/images/formula4.png)

它的导数为：
[Alt text](../../resources/_gen/images/formula5.png)

那么梯度反向[Alt text](../../resources/_gen/images/formula6.png)。
我们从一个初始点[Alt text](../../resources/_gen/images/formula7.png)开始进行迭代，每次令
[Alt text](../../resources/_gen/images/formula8.png)
得到一个新的点[Alt text](../../resources/_gen/images/formula9.png)，其中 α 为学习率（learning rate）。

当迭代了一定次数之后，当前的点会非常接近真正的最小值点，如果我们的学习速率保持不变，迭代的结果将会在最小值点的周围来回「震荡」，无法继续接近最小值点。因此，我们需要设置学习率衰减（learning rate decay），在迭代的过程中逐步减小学习率，向最小值点逼近。


三分法：C++代码转rust
```
impl Solution {
    pub fn get_min_dist_sum(positions: Vec<Vec<i32>>) -> f64 {
        let eps = 1e-7;

        // 计算服务中心 (xc, yc) 到客户的欧几里得距离之和
        let get_dist = |xc: f64, yc: f64| {
            let mut ans = 0.0;
            for pos in &positions {
                let dx = pos[0] as f64 - xc;
                let dy = pos[1] as f64 - yc;
                ans += (dx * dx + dy * dy).sqrt();
            }
            ans
        };

        // 固定 xc，使用三分法找出最优的 yc
        let check_optimal = |xc: f64| {
            let mut y_left = 0.0;
            let mut y_right = 100.0;
            while y_right - y_left > eps {
                let y_first = (y_left + y_left + y_right) / 3.0;
                let y_second = (y_left + y_right + y_right) / 3.0;
                if get_dist(xc, y_first) < get_dist(xc, y_second) {
                    y_right = y_second;
                } else {
                    y_left = y_first;
                }
            }
            get_dist(xc, y_left)
        };
        
        let mut x_left = 0.0;
        let mut x_right = 100.0;
        while x_right - x_left > eps {
            // 左 1/3 点
            let x_first = (x_left + x_left + x_right) / 3.0;
            // 右 1/3 点
            let x_second = (x_left + x_right + x_right) / 3.0;
            if check_optimal(x_first) < check_optimal(x_second) {
                x_right = x_second;
            } else {
                x_left = x_first;
            }
        }

        check_optimal(x_left)
    }
}
```

deepseek给出的rust代码
```
impl Solution {
    pub fn get_min_dist_sum(positions: Vec<Vec<i32>>) -> f64 {
        let eps = 1e-7;

        // 计算服务中心 (xc, yc) 到客户的欧几里得距离之和（函数式版本）
        let get_dist = |xc: f64, yc: f64| {
            positions.iter()
                .map(|pos| {
                    let dx = pos[0] as f64 - xc;
                    let dy = pos[1] as f64 - yc;
                    (dx * dx + dy * dy).sqrt()
                })
                .sum()
        };

        // 三分法查找最优值（递归实现）
        fn ternary_search<F>(mut left: f64, mut right: f64, eps: f64, f: F) -> f64 
        where
            F: Fn(f64) -> f64,
        {
            while right - left > eps {
                let first = (2.0 * left + right) / 3.0;
                let second = (left + 2.0 * right) / 3.0;
                if f(first) < f(second) {
                    right = second;
                } else {
                    left = first;
                }
            }
            f(left)
        }

        // 外层三分法查找最优 xc，内层对每个 xc 使用三分法查找最优 yc
        ternary_search(0.0, 100.0, eps, |xc| {
            ternary_search(0.0, 100.0, eps, |yc| get_dist(xc, yc))
        })
    }
}
```
deepseek改进说明：

get_dist 函数：
使用 iter() 和 map() 替代显式循环
使用 sum() 替代手动累加
三分法抽象：
将三分法提取为通用的 ternary_search 函数
使用闭包参数使其可以应用于任何需要优化的函数
递归调用替代原来的嵌套循环结构
主逻辑简化：
外层三分法调用内层三分法，形成清晰的函数组合
消除了显式的可变状态（mut 变量）
代码结构：
更符合函数式编程的无副作用原则
高阶函数的使用使算法逻辑更清晰
减少了中间变量的使用