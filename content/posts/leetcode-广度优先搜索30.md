---
title: "leetcode-广度优先搜索30"
date: 2026-09-13T09:32:08+08:00
tags: ["leetcode", "广度优先搜索"]
draft: false
---


## 跳跃游戏 III

给定一个非负整数数组 arr，你最开始位于该数组的起始下标 start 处。当你位于下标 i 处时，你可以跳到 i + arr[i] 或者 i - arr[i]。

请你判断自己是否能够跳到对应元素值为 0 的 任一 下标处。

注意，不管是什么情况下，你都无法跳到数组之外。


```
use std::collections::VecDeque;

impl Solution {
    pub fn can_reach(arr: Vec<i32>, start: i32) -> bool {
        let n = arr.len();
        let start = start as usize;

        // BFS 从 start 出发，访问过的下标不再重复入队
        let mut visited = vec![false; n];
        visited[start] = true;

        let mut queue = VecDeque::new();
        queue.push_back(start);

        while let Some(i) = queue.pop_front() {
            // 到达值为 0 的下标即可返回
            if arr[i] == 0 {
                return true;
            }

            // 从 i 可以跳到 i + arr[i] 或 i - arr[i]
            for next in [i as i32 + arr[i], i as i32 - arr[i]] {
                if next < 0 || next >= n as i32 {
                    continue;
                }
                let next = next as usize;
                if !visited[next] {
                    visited[next] = true;
                    queue.push_back(next);
                }
            }
        }

        false
    }
}
```
