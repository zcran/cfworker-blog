---
title: "leetcode-广度优先搜索39"
date: 2026-09-13T09:32:08+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 转化数字的最小运算数

给你一个下标从 0 开始的整数数组 nums ，该数组由 互不相同 的数字组成。另给你两个整数 start 和 goal 。

整数 x 的值最开始设为 start ，你打算执行一些运算使 x 转化为 goal 。你可以对数字 x 重复执行下述运算：

如果 0 <= x <= 1000 ，那么，对于数组中的任一下标 i（0 <= i < nums.length），可以将 x 设为下述任一值：

x + nums[i]
x - nums[i]
x ^ nums[i]（按位异或 XOR）

注意，你可以按任意顺序使用每个 nums[i] 任意次。使 x 越过 0 <= x <= 1000 范围的运算同样可以生效，但该该运算执行后将不能执行其他运算。

返回将 x = start 转化为 goal 的最小操作数；如果无法完成转化，则返回 -1 。


```
use std::collections::VecDeque;

impl Solution {
    pub fn minimum_operations(nums: Vec<i32>, start: i32, goal: i32) -> i32 {
        // 若起点即目标，直接返回 0
        if start == goal {
            return 0;
        }

        // 操作范围 0..=1000，超出该范围的数不再继续扩展
        const LIMIT: i32 = 1000;
        let mut visited = vec![false; (LIMIT + 1) as usize];
        visited[start as usize] = true;

        let mut queue = VecDeque::new();
        queue.push_back((start, 0));

        while let Some((x, step)) = queue.pop_front() {
            for &num in &nums {
                // 三种操作：加、减、异或
                for nx in [x + num, x - num, x ^ num] {
                    if nx == goal {
                        return step + 1;
                    }
                    // 只有在 [0, 1000] 范围内的新值才继续扩展
                    if (0..=LIMIT).contains(&nx) && !visited[nx as usize] {
                        visited[nx as usize] = true;
                        queue.push_back((nx, step + 1));
                    }
                }
            }
        }

        -1
    }
}
```
