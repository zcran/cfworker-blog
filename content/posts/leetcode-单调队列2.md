---
title: "leetcode-单调队列2"
date: 2026-03-31T20:55:28+08:00
tags: ["leetcode", "单调队列"]
draft: false
---

## 和至少为 K 的最短子数组

给你一个整数数组 nums 和一个整数 k ，找出 nums 中和至少为 k 的 最短非空子数组 ，并返回该子数组的长度。如果不存在这样的 子数组 ，返回 -1 。

子数组 是数组中 连续 的一部分。

```
impl Solution {
    pub fn shortest_subarray(nums: Vec<i32>, k: i32) -> i32 {
        use std::collections::VecDeque;

        // 使用 fold 进行函数式迭代，维护状态 (前缀和, 最短长度, 单调队列)
        let (_, ret, _) = nums
            .iter()
            .enumerate()
            .fold(
                (0i64, i64::MAX, VecDeque::from([(0i64, -1i32)])),
                |(mut pre_sum, mut ret, mut queue), (i, &num)| {
                    // 更新前缀和
                    pre_sum += num as i64;

                    // 维护单调递增队列：弹出队尾所有前缀和 >= 当前前缀和的元素
                    while let Some(&(last_prefix, _)) = queue.back() {
                        if pre_sum <= last_prefix {
                            queue.pop_back();
                        } else {
                            break;
                        }
                    }

                    // 检查并更新最短长度：弹出队头所有满足条件的前缀和
                    while let Some(&(first_prefix, idx)) = queue.front() {
                        if pre_sum - first_prefix >= k as i64 {
                            ret = ret.min(i as i64 - idx as i64);
                            queue.pop_front();
                        } else {
                            break;
                        }
                    }

                    // 将当前前缀和加入队尾
                    queue.push_back((pre_sum, i as i32));

                    (pre_sum, ret, queue)
                },
            );

        // 返回结果
        if ret == i64::MAX { -1 } else { ret as i32 }
    }
}
```
