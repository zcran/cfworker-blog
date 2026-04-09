---
title: "leetcode-单调队列22"
date: 2026-03-31T20:55:29+08:00
tags: ["leetcode", "单调队列"]
draft: false
---


## 望远镜中最高的海拔

科技馆内有一台虚拟观景望远镜，它可以用来观测特定纬度地区的地形情况。该纬度的海拔数据记于数组 heights ，其中 heights[i] 表示对应位置的海拔高度。请找出并返回望远镜视野范围 limit 内，可以观测到的最高海拔值。

示例 1：

输入：heights = [14,2,27,-5,28,13,39], limit = 3
输出：[27,27,28,28,39]
解释：
  滑动窗口的位置                最大值
---------------               -----
[14 2 27] -5 28 13 39          27
14 [2 27 -5] 28 13 39          27
14 2 [27 -5 28] 13 39          28
14 2 27 [-5 28 13] 39          28
14 2 27 -5 [28 13 39]          39


```
use std::collections::VecDeque;

impl Solution {
    pub fn max_altitude(heights: Vec<i32>, limit: i32) -> Vec<i32> {
        let k = limit as usize;
        let mut ans = Vec::with_capacity(heights.len() - k + 1); // 预先分配好空间
        let mut q = VecDeque::new(); // 双端队列
        for (i, &x) in heights.iter().enumerate() {
            // 1. 入
            while !q.is_empty() && heights[*q.back().unwrap()] <= x {
                q.pop_back(); // 维护 q 的单调性
            }
            q.push_back(i); // 入队
            // 2. 出
            if i - q[0] >= k { // 队首已经离开窗口了
                q.pop_front();
            }
            // 3. 记录答案
            if i >= k - 1 {
                // 由于队首到队尾单调递减，所以窗口最大值就是队首
                ans.push(heights[q[0]]);
            }
        }
        ans
    }
}

```
