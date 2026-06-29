---
title: "leetcode-单调栈1"
date: 2026-06-07T10:14:03+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 接雨水

给定 n 个非负整数表示每个宽度为 1 的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。

```
impl Solution {
    pub fn trap(height: Vec<i32>) -> i32 {
        // 边界条件：空数组无法接水
        if height.is_empty() {
            return 0;
        }

        // 双指针：left 从左向右移动，right 从右向左移动
        let mut left = 0;
        let mut right = height.len() - 1;

        // left_max：从左侧扫描到当前位置的最大高度
        // right_max：从右侧扫描到当前位置的最大高度
        let mut left_max = 0;
        let mut right_max = 0;

        let mut water = 0;  // 累计接水量

        // 当左右指针未相遇时继续处理
        while left < right {
            // 关键原理：当前位置能接的水量取决于 min(左侧最高, 右侧最高)
            // 如果 height[left] < height[right]，则 min(左侧最高, 右侧最高) 一定等于 left_max
            // 因为 right_max >= height[right] > height[left] >= left_max
            // 所以此时处理左指针位置的水量是安全的
            if height[left] < height[right] {
                // 处理左指针位置
                if height[left] >= left_max {
                    // 当前高度大于等于左侧最高，无法存水，更新左侧最高
                    left_max = height[left];
                } else {
                    // 当前高度低于左侧最高，可以存水
                    // 存水量 = 左侧最高 - 当前高度（因为右侧肯定更高）
                    water += left_max - height[left];
                }
                left += 1;  // 左指针右移
            } else {
                // 处理右指针位置（包括 height[left] >= height[right] 的情况）
                // 对称原理：此时 min(左侧最高, 右侧最高) 一定等于 right_max
                if height[right] >= right_max {
                    // 当前高度大于等于右侧最高，无法存水，更新右侧最高
                    right_max = height[right];
                } else {
                    // 当前高度低于右侧最高，可以存水
                    // 存水量 = 右侧最高 - 当前高度（因为左侧肯定更高或相等）
                    water += right_max - height[right];
                }
                right -= 1;  // 右指针左移
            }
        }

        water  // 返回总接水量
    }
}
```


```
use std::iter::FromIterator;  // 用于将迭代器转换为 Vec

impl Solution {
    pub fn trap(height: Vec<i32>) -> i32 {
        // 第一步：从左到右扫描，计算每个位置的左侧最高值
        // scan(0, |state, x| {...}) 中 state 累积的是到当前位置为止的左侧最高值
        // 返回元组 (左侧最高值, 当前高度)
        Vec::from_iter(
            height.into_iter()
                .scan(0, |state, x| {
                    *state = x.max(*state);  // 更新左侧最高值（当前高度与历史最高取大）
                    Some((*state, x))        // 输出：(左侧最高, 当前高度)
                })
        )
        // 第二步：反转数组，从右到左扫描，计算每个位置的水量
        .into_iter()
        .rev()  // 反转，让右侧最高值的累积成为可能
        .scan(0, |state, (left_max, height)| {
            Some({
                // state 在反向扫描中累积的是右侧最高值
                // left_max.min(*state) 得到 min(左侧最高, 右侧最高)
                // .max(height) 确保结果不小于当前高度（实际就是 min(左max,右max)）
                *state = left_max.min(*state).max(height);
                // 当前位置的积水量 = min(左max,右max) - 当前高度
                *state - height
            })
        })
        .sum()  // 累加所有位置的积水量
    }
}
```
