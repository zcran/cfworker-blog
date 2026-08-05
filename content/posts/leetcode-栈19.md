---
title: "leetcode-栈19"
date: 2026-07-24T10:16:58+08:00
tags: ["leetcode", "栈"]
draft: false
---


## 小行星碰撞

给定一个整数数组 asteroids，表示在同一行的小行星。数组中小行星的索引表示它们在空间中的相对位置。

对于数组中的每一个元素，其绝对值表示小行星的大小，正负表示小行星的移动方向（正表示向右移动，负表示向左移动）。每一颗小行星以相同的速度移动。

找出碰撞后剩下的所有小行星。碰撞规则：两个小行星相互碰撞，较小的小行星会爆炸。如果两颗小行星大小相同，则两颗小行星都会爆炸。两颗移动方向相同的小行星，永远不会发生碰撞。


```
// 小行星碰撞模拟
// 使用栈存储幸存小行星，只有向左的（负）会与向右的（正）碰撞
impl Solution {
    pub fn asteroid_collision(asteroids: Vec<i32>) -> Vec<i32> {
        let mut survivors = Vec::with_capacity(asteroids.len());

        for asteroid in asteroids {
            // 当前小行星向右移动：永远不会与栈顶碰撞，直接入栈
            if asteroid > 0 {
                survivors.push(asteroid);
                continue;
            }

            let mut current = asteroid;
            let mut exploded = false;

            // 只有当栈顶向右移动（正数）时才会发生碰撞
            while let Some(&top) = survivors.last() {
                if top < 0 {
                    break; // 栈顶向左，方向相同，不会碰撞
                }

                // 比较大小
                if top < -current {
                    // 栈顶较小，爆炸，继续与下一个比较
                    survivors.pop();
                } else if top == -current {
                    // 大小相同，两者都爆炸
                    survivors.pop();
                    exploded = true;
                    break;
                } else {
                    // current 较小，当前小行星爆炸
                    exploded = true;
                    break;
                }
            }

            // 如果当前小行星没有爆炸，入栈
            if !exploded {
                survivors.push(current);
            }
        }

        survivors
    }
}
```
