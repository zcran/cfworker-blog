---
title: "leetcode-单调栈41"
date: 2026-06-07T10:14:05+08:00
tags: ["leetcode", "单调栈"]
draft: false
---


## 美丽塔 I

给定一个包含 n 个整数的数组 heights 表示 n 座连续的塔中砖块的数量。你的任务是移除一些砖块来形成一个 山脉状 的塔排列。在这种布置中，塔高度先是非递减，有一个或多个连续塔达到最大峰值，然后非递增排列。

返回满足山脉状塔排列的方案中，高度和的最大值 。


```
impl Solution {
    pub fn maximum_sum_of_heights(max_heights: Vec<i32>) -> i64 {
        let n = max_heights.len();
        let h: Vec<i64> = max_heights.iter().map(|&x| x as i64).collect();

        // prefix[i] = 以 i 为峰顶时，左侧部分的最大和（包含 i）
        let mut prefix = vec![0; n];
        let mut stack: Vec<usize> = Vec::new();

        for i in 0..n {
            while let Some(&top) = stack.last() {
                if h[top] >= h[i] {
                    stack.pop();
                } else {
                    break;
                }
            }

            if let Some(&top) = stack.last() {
                // 左边有更小的元素，从那个位置延续
                prefix[i] = prefix[top] + h[i] * (i - top) as i64;
            } else {
                // 左边没有更小的，全部设为 h[i]
                prefix[i] = h[i] * (i + 1) as i64;
            }
            stack.push(i);
        }

        // suffix[i] = 以 i 为峰顶时，右侧部分的最大和（包含 i）
        let mut suffix = vec![0; n];
        stack.clear();

        for i in (0..n).rev() {
            while let Some(&top) = stack.last() {
                if h[top] >= h[i] {
                    stack.pop();
                } else {
                    break;
                }
            }

            if let Some(&top) = stack.last() {
                suffix[i] = suffix[top] + h[i] * (top - i) as i64;
            } else {
                suffix[i] = h[i] * (n - i) as i64;
            }
            stack.push(i);
        }

        // 峰值被计算了两次，减去一次
        let mut ans = 0i64;
        for i in 0..n {
            ans = ans.max(prefix[i] + suffix[i] - h[i]);
        }

        ans
    }
}
```
