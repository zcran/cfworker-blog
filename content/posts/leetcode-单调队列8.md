---
title: "leetcode-单调队列8"
date: 2026-03-31T20:55:29+08:00
tags: ["leetcode", "单调队列"]
draft: false
---


## 跳跃游戏 VI

给你一个下标从 0 开始的整数数组 nums 和一个整数 k 。

一开始你在下标 0 处。每一步，你最多可以往前跳 k 步，但你不能跳出数组的边界。也就是说，你可以从下标 i 跳到 [i + 1， min(n - 1, i + k)] 包含 两个端点的任意位置。

你的目标是到达数组最后一个位置（下标为 n - 1 ），你的 得分 为经过的所有数字之和。

请你返回你能得到的 最大得分 。

```
use std::collections::BinaryHeap;

impl Solution {
    pub fn max_result(nums: Vec<i32>, k: i32) -> i32 {
        let n = nums.len();

        nums.into_iter()
            .enumerate()
            .fold(
                (BinaryHeap::new(), 0),
                |(mut heap, _), (i, x)| {
                    let i = i as i32;

                    // 清理过期元素
                    while let Some(&(_, idx)) = heap.peek() {
                        if i - idx > k {
                            heap.pop();
                        } else {
                            break;
                        }
                    }

                    // 获取最大得分
                    let cur = heap.peek().map(|&(score, _)| score).unwrap_or(0);

                    // 加入当前位置
                    heap.push((cur + x, i));
                    (heap, cur)
                },
            )
            .0
            .into_iter()
            .find(|&(_, idx)| idx == n as i32 - 1)
            .map(|(score, _)| score)
            .unwrap_or(0)
    }
}
```
