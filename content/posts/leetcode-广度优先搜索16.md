---
title: "leetcode-广度优先搜索16"
date: 2026-09-13T09:32:07+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 打开转盘锁

你有一个带有四个圆形拨轮的转盘锁。每个拨轮都有10个数字： '0', '1', '2', '3', '4', '5', '6', '7', '8', '9' 。每个拨轮可以自由旋转：例如把 '9' 变为 '0'，'0' 变为 '9' 。每次旋转都只能旋转一个拨轮的一位数字。

锁的初始数字为 '0000' ，一个代表四个拨轮的数字的字符串。

列表 deadends 包含了一组死亡数字，一旦拨轮的数字和列表里的任何一个元素相同，这个锁将会被永久锁定，无法再被旋转。

字符串 target 代表可以解锁的数字，你需要给出解锁需要的最小旋转次数，如果无论如何不能解锁，返回 -1 。


```
use std::collections::{HashSet, VecDeque};

impl Solution {
    pub fn open_lock(deadends: Vec<String>, target: String) -> i32 {
        // 死亡集合复用为 visited 集合，用 u16 表示 4 位数字更快更省内存
        let mut visited: HashSet<u16> = deadends.iter().map(|s| s.parse().unwrap()).collect();
        let start: u16 = 0;
        let target: u16 = target.parse().unwrap();

        if visited.contains(&start) {
            return -1;
        }
        if start == target {
            return 0;
        }

        // 生成旋转一位后的 8 个相邻状态
        fn neighbors(num: u16) -> [u16; 8] {
            let mut res = [0u16; 8];
            let mut idx = 0;
            let mut exp = 1u16;
            for _ in 0..4 {
                let digit = num / exp % 10;
                // 当前位 +1（9 -> 0）
                res[idx] = if digit == 9 { num - 9 * exp } else { num + exp };
                idx += 1;
                // 当前位 -1（0 -> 9）
                res[idx] = if digit == 0 { num + 9 * exp } else { num - exp };
                idx += 1;
                exp *= 10;
            }
            res
        }

        // 单源 BFS，visited 复用 dead 集合，避免额外集合
        let mut queue: VecDeque<u16> = VecDeque::new();
        queue.push_back(start);
        visited.insert(start);

        let mut step = 0;
        while !queue.is_empty() {
            step += 1;
            for _ in 0..queue.len() {
                let cur = queue.pop_front().unwrap();
                for next in neighbors(cur) {
                    if next == target {
                        return step;
                    }
                    // 未访问过（即不在 dead 集合中）则入队
                    if visited.insert(next) {
                        queue.push_back(next);
                    }
                }
            }
        }

        -1
    }
}
```
