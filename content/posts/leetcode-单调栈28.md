---
title: "leetcode-单调栈28"
date: 2026-06-07T10:14:05+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 车队 II

在一条单车道上有 n 辆车，它们朝着同样的方向行驶。给你一个长度为 n 的数组 cars ，其中 cars[i] = [positioni, speedi] ，它表示：

positioni 是第 i 辆车和道路起点之间的距离（单位：米）。题目保证 positioni < positioni+1 。
speedi 是第 i 辆车的初始速度（单位：米/秒）。
简单起见，所有车子可以视为在数轴上移动的点。当两辆车占据同一个位置时，我们称它们相遇了。一旦两辆车相遇，它们会合并成一个车队，这个车队里的车有着同样的位置和相同的速度，速度为这个车队里 最慢 一辆车的速度。

请你返回一个数组 answer ，其中 answer[i] 是第 i 辆车与下一辆车相遇的时间（单位：秒），如果这辆车不会与下一辆车相遇，则 answer[i] 为 -1 。答案精度误差需在 10^-5 以内。


```
impl Solution {
    /// 碰撞时间计算（单调栈优化版）
    ///
    /// # 优化点
    /// 1. 使用元组避免重复索引 cars
    /// 2. 预计算浮点数，减少类型转换
    /// 3. 栈存储索引，但缓存位置和速度
    pub fn get_collision_times(cars: Vec<Vec<i32>>) -> Vec<f64> {
        let n = cars.len();
        let mut ans = vec![-1.0; n];
        let mut stack = Vec::with_capacity(n);

        // 预先转换为浮点数，避免重复转换
        let pos: Vec<f64> = cars.iter().map(|c| c[0] as f64).collect();
        let speed: Vec<f64> = cars.iter().map(|c| c[1] as f64).collect();

        for i in (0..n).rev() {
            // 移除所有速度 >= 当前车的（永远追不上）
            while let Some(&top) = stack.last() && speed[top] >= speed[i] {
                stack.pop();
            }

            // 寻找真正的碰撞目标
            while let Some(&top) = stack.last() {
                if ans[top] < 0.0 {
                    break;  // 栈顶车不会消失
                }

                let catch_time = (pos[top] - pos[i]) / (speed[i] - speed[top]);
                if catch_time <= ans[top] {
                    break;  // 能在消失前追上
                }
                stack.pop();  // 追不上，继续找
            }

            // 计算碰撞时间
            if let Some(&top) = stack.last() {
                ans[i] = (pos[top] - pos[i]) / (speed[i] - speed[top]);
            }

            stack.push(i);
        }

        ans
    }
}
```
