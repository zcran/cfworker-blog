---
title: "leetcode-数学107"
date: 2026-10-07T20:39:35+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 数字游戏

小扣在秋日市集入口处发现了一个数字游戏。主办方共有 N 个计数器，计数器编号为 0 ~ N-1。每个计数器上分别显示了一个数字，小扣按计数器编号升序将所显示的数字记于数组 nums。每个计数器上有两个按钮，分别可以实现将显示数字加一或减一。小扣每一次操作可以选择一个计数器，按下加一或减一按钮。

主办方请小扣回答出一个长度为 N 的数组，第 i 个元素(0 <= i < N)表示将 0~i 号计数器 初始 所示数字操作成满足所有条件 nums[a]+1 == nums[a+1],(0 <= a < i) 的最小操作数。回答正确方可进入秋日市集。

由于答案可能很大，请将每个最小操作数对 1,000,000,007 取余。


```
use std::cmp::Reverse;
use std::collections::BinaryHeap;

impl Solution {
    pub fn nums_game(nums: Vec<i32>) -> Vec<i32> {
        const MOD: i64 = 1_000_000_007;
        let n = nums.len();
        let mut ans = vec![0i32; n];

        // 对前缀 i，令 b_k = nums[k] - k，目标是让 b 全部相等（中位数最优）
        // left：较小一半（大根堆），right：较大一半（小根堆）
        let mut left: BinaryHeap<i32> = BinaryHeap::new();
        let mut right: BinaryHeap<Reverse<i32>> = BinaryHeap::new();
        let mut left_sum: i64 = 0;
        let mut right_sum: i64 = 0;

        for (i, &num) in nums.iter().enumerate() {
            let mut b = num - i as i32;

            if i % 2 == 0 {
                // 前缀长度为奇数，多出的元素放入 right
                if let Some(&top) = left.peek() {
                    if b < top {
                        left_sum -= (top - b) as i64;
                        left.push(b);
                        b = left.pop().unwrap();
                    }
                }
                right_sum += b as i64;
                right.push(Reverse(b));
                // 中位数是 right 堆顶
                let med = right.peek().unwrap().0 as i64;
                ans[i] = ((right_sum - med - left_sum) % MOD) as i32;
            } else {
                // 前缀长度为偶数，两堆大小相等
                let top = right.peek().unwrap().0;
                if b > top {
                    right_sum += (b - top) as i64;
                    right.push(Reverse(b));
                    b = right.pop().unwrap().0;
                }
                left_sum += b as i64;
                left.push(b);
                ans[i] = ((right_sum - left_sum) % MOD) as i32;
            }
        }

        ans
    }
}
```
