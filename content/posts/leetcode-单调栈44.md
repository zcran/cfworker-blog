---
title: "leetcode-单调栈44"
date: 2026-06-07T10:14:05+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


##  使数组等于目标数组所需的最少操作次数

给你两个长度相同的正整数数组 nums 和 target。

在一次操作中，你可以选择 nums 的任何子数组，并将该子数组内的每个元素的值增加或减少 1。

返回使 nums 数组变为 target 数组所需的 最少 操作次数。

```
impl Solution {
    /// 最少操作次数
    ///
    /// # 算法原理
    /// 1. 定义 diff[i] = target[i] - nums[i]，表示每个位置需要变化的量
    /// 2. 考虑相邻 diff 的差值 d[i] = diff[i] - diff[i-1]
    /// 3. 正差值 d[i] 表示需要额外增加的操作，负差值表示需要额外减少的操作
    /// 4. 总操作次数 = max(所有正差值和, 所有负差值绝对值之和)
    ///
    /// # 为什么这样正确？
    /// 因为对子数组 +1 操作等价于 diff 数组的一段区间 +1
    /// 这相当于对差分数组 d 进行单点操作（左端点 +1，右端点+1 处 -1）
    /// 最终需要将 d 全部变为 0，所需操作次数就是 max(正数和, 负数和绝对值)
    pub fn minimum_operations(nums: Vec<i32>, target: Vec<i32>) -> i64 {
        let mut inc = 0i64;  // 需要增加的操作总次数
        let mut dec = 0i64;  // 需要减少的操作总次数
        let mut prev = 0i64; // 前一个位置的 diff 值

        for (&n, &t) in nums.iter().zip(target.iter()) {
            let curr = (t - n) as i64;  // 当前位置的差值
            let delta = curr - prev;     // 差分数组的值

            if delta > 0 {
                inc += delta;  // 需要增加
            } else {
                dec -= delta;  // delta 为负，取绝对值
            }

            prev = curr;
        }

        inc.max(dec)
    }
}
```


```
impl Solution {
    pub fn minimum_operations(nums: Vec<i32>, target: Vec<i32>) -> i64 {
        let diffs: Vec<i64> = nums.iter()
            .zip(target.iter())
            .map(|(&n, &t)| (t - n) as i64)
            .collect();

        let (pos, neg) = diffs.iter()
            .scan(0, |prev, &curr| {
                let delta = curr - *prev;
                *prev = curr;
                Some(delta)
            })
            .fold((0i64, 0i64), |(pos, neg), delta| {
                if delta > 0 {
                    (pos + delta, neg)
                } else {
                    (pos, neg - delta)
                }
            });

        pos.max(neg)
    }
}
```
