---
title: "leetcode-单调队列13"
date: 2026-03-31T20:55:29+08:00
tags: ["leetcode", "单调队列"]
draft: false
---


## 不间断子数组

给你一个下标从 0 开始的整数数组 nums 。nums 的一个子数组如果满足以下条件，那么它是 不间断 的：

i，i + 1 ，...，j  表示子数组中的下标。对于所有满足 i <= i1, i2 <= j 的下标对，都有 0 <= |nums[i1] - nums[i2]| <= 2 。
请你返回 不间断 子数组的总数目。

子数组是一个数组中一段连续 非空 的元素序列。

```
use std::collections::VecDeque;

impl Solution {
    pub fn continuous_subarrays(nums: Vec<i32>) -> i64 {
        let mut min_deque: VecDeque<usize> = VecDeque::new(); // 存储索引，值递增
        let mut max_deque: VecDeque<usize> = VecDeque::new(); // 存储索引，值递减
        let mut left = 0;
        let mut result = 0i64;

        for right in 0..nums.len() {
            // 1. 维护最小值的单调队列
            // 保持队列中的值递增，新元素更小时弹出队尾
            while let Some(&last) = min_deque.back() {
                if nums[last] >= nums[right] {
                    min_deque.pop_back();
                } else {
                    break;
                }
            }
            min_deque.push_back(right);

            // 2. 维护最大值的单调队列
            // 保持队列中的值递减，新元素更大时弹出队尾
            while let Some(&last) = max_deque.back() {
                if nums[last] <= nums[right] {
                    max_deque.pop_back();
                } else {
                    break;
                }
            }
            max_deque.push_back(right);

            // 3. 收缩窗口直到满足条件 max - min <= 2
            // 注意：这里使用模式匹配直接获取值，不需要 unwrap
            while let (Some(&min_idx), Some(&max_idx)) = (min_deque.front(), max_deque.front()) {
                if nums[max_idx] - nums[min_idx] <= 2 {
                    break;
                }

                // 左边界元素要被移出窗口，检查是否需要从队列中移除
                if max_idx == left {
                    max_deque.pop_front();
                }
                if min_idx == left {
                    min_deque.pop_front();
                }
                left += 1;
            }

            // 4. 所有以 right 结尾的子数组都满足条件
            // 子数组数量 = right - left + 1
            result += (right - left + 1) as i64;
        }

        result
    }
}
```
