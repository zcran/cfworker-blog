---
title: "leetcode-单调队列18"
date: 2026-03-31T20:55:29+08:00
tags: ["leetcode", "单调队列"]
draft: false
---


## 计数质数间隔平衡子数组

给定一个整数数组 nums 和一个整数 k。

子数组 被称为 质数间隔平衡，如果：

其包含 至少两个质数，并且
该 子数组 中 最大 和 最小 质数的差小于或等于 k。
返回 nums 中质数间隔平衡子数组的数量。

注意：

子数组 是数组中连续的 非空 元素序列。
质数是大于 1 的自然数，它只有两个因数，即 1 和它本身。


```
impl Solution {
    pub fn prime_subarray(nums: Vec<i32>, k: i32) -> i32 {
        const MX: usize = 50_001;

        // 使用静态数组进行质数筛（通过once_cell或lazy_static，这里简化处理）
        let mut np = vec![false; MX];
        np[1] = true; // 1不是质数
        for i in 2..(MX as f64).sqrt() as usize + 1 {
            if !np[i] {
                let mut j = i * i;
                while j < MX {
                    np[j] = true;
                    j += i;
                }
            }
        }

        let mut min_q: std::collections::VecDeque<usize> = std::collections::VecDeque::new();
        let mut max_q: std::collections::VecDeque<usize> = std::collections::VecDeque::new();
        let mut last = -1i32;
        let mut last2 = -1i32;
        let mut ans = 0i32;
        let mut left = 0usize;

        for (i, &x) in nums.iter().enumerate() {
            if !np[x as usize] {
                // 1. 入队
                last2 = last;
                last = i as i32;

                // 维护单调递增队列（最小值）
                while let Some(&back) = min_q.back() {
                    if x <= nums[back] {
                        min_q.pop_back();
                    } else {
                        break;
                    }
                }
                min_q.push_back(i);

                // 维护单调递减队列（最大值）
                while let Some(&back) = max_q.back() {
                    if x >= nums[back] {
                        max_q.pop_back();
                    } else {
                        break;
                    }
                }
                max_q.push_back(i);

                // 2. 出队：收缩窗口直到满足条件
                while let (Some(&max_front), Some(&min_front)) = (max_q.front(), min_q.front()) {
                    if nums[max_front] - nums[min_front] > k {
                        left += 1;
                        if min_front < left {
                            min_q.pop_front();
                        }
                        if max_front < left {
                            max_q.pop_front();
                        }
                    } else {
                        break;
                    }
                }
            }

            // 3. 更新答案
            if last2 >= left as i32 {
                ans += last2 - left as i32 + 1;
            }
        }

        ans
    }
}
```
