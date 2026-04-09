---
title: "leetcode-单调队列3"
date: 2026-03-31T20:55:29+08:00
tags: ["leetcode", "单调队列"]
draft: false
---

## 环形子数组的最大和

给定一个长度为 n 的环形整数数组 nums ，返回 nums 的非空 子数组 的最大可能和 。

环形数组 意味着数组的末端将会与开头相连呈环状。形式上， nums[i] 的下一个元素是 nums[(i + 1) % n] ， nums[i] 的前一个元素是 nums[(i - 1 + n) % n] 。

子数组 最多只能包含固定缓冲区 nums 中的每个元素一次。形式上，对于子数组 nums[i], nums[i + 1], ..., nums[j] ，不存在 i <= k1, k2 <= j 其中 k1 % n == k2 % n 。

```
impl Solution {
    pub fn max_subarray_sum_circular(nums: Vec<i32>) -> i32 {
        #[derive(Clone, Copy)]
        struct State {
            global_max: i32,
            global_min: i32,
            curr_max: i32,
            curr_min: i32,
            total: i32,
        }

        let State { global_max, global_min, total, .. } = nums
            .iter()
            .enumerate()
            .fold(
                State {
                    global_max: nums[0],
                    global_min: nums[0],
                    curr_max: 0,
                    curr_min: 0,
                    total: 0,
                },
                |state, (i, &x)| {
                    let curr_max = if i == 0 { x } else { x.max(state.curr_max + x) };
                    let curr_min = if i == 0 { x } else { x.min(state.curr_min + x) };

                    State {
                        global_max: state.global_max.max(curr_max),
                        global_min: state.global_min.min(curr_min),
                        curr_max,
                        curr_min,
                        total: state.total + x,
                    }
                },
            );

        if global_max > 0 {
            global_max.max(total - global_min)
        } else {
            global_max
        }
    }
}
```
