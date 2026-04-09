---
title: "leetcode-单调队列4"
date: 2026-03-31T20:55:29+08:00
tags: ["leetcode", "单调队列"]
draft: false
---


## 带限制的子序列和

给你一个整数数组 nums 和一个整数 k ，请你返回 非空 子序列元素和的最大值，子序列需要满足：子序列中每两个 相邻 的整数 nums[i] 和 nums[j] ，它们在原数组中的下标 i 和 j 满足 i < j 且 j - i <= k 。

数组的子序列定义为：将数组中的若干个数字删除（可以删除 0 个数字），剩下的数字按照原本的顺序排布。



```
use std::collections::VecDeque;

impl Solution {
    pub fn constrained_subset_sum(nums: Vec<i32>, k: i32) -> i32 {
        let k = k as usize;

        // 使用 fold 进行函数式迭代，维护状态 (dp结果数组, 单调队列, 全局最大值)
        let (dp, _, ans) = nums
            .iter()
            .enumerate()
            .fold(
            (vec![0; nums.len()], VecDeque::new(), i32::MIN),
            |(mut dp, mut queue, mut ans), (i, &num)| {
                if i == 0 {
                    // 处理第一个元素
                    dp[i] = num;
                    queue.push_back(i);
                    ans = ans.max(dp[i]);
                    (dp, queue, ans)
                } else {
                    // 移除超出窗口范围的索引
                    while let Some(&front) = queue.front() {
                        if i - front > k {
                            queue.pop_front();
                        } else {
                            break;
                        }
                    }

                    // 获取最优的前缀和（队首元素对应的 dp 值）
                    let best_prev = if let Some(&front) = queue.front() {
                        dp[front].max(0)
                    } else {
                        0
                    };

                    // 计算当前 dp 值
                    dp[i] = best_prev + num;
                    ans = ans.max(dp[i]);

                    // 维护单调递减队列（队首最大）
                    while let Some(&back) = queue.back() {
                        if dp[i] >= dp[back] {
                            queue.pop_back();
                        } else {
                            break;
                        }
                    }

                    queue.push_back(i);
                    (dp, queue, ans)
                }
            },
        );

        ans
    }
}
```
