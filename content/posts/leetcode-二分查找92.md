---
title: "leetcode-二分查找92"
date: 2026-09-28T12:29:16+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 统计影子数对 II

给你一个长度为 n 的整数数组 nums。

如果一对下标 (i, j) 满足以下所有条件，则称其为一个影子对：

0 <= i < j < n
nums[i] < nums[j]
不存在下标 k，使得 i < k < j 且 nums[i] < nums[k] < nums[j]。

返回影子对的总数。


```
impl Solution {
    pub fn shadow_pairs(mut nums: Vec<i32>) -> i32 {
        // 离散化
        let mut sorted = nums.clone();
        sorted.sort_unstable();
        sorted.dedup();
        for x in nums.iter_mut() {
            *x = sorted.partition_point(|&v| v < *x) as i32;
        }
        let max_val = sorted.len() as i32 - 1;
        Self::solve(&nums, 0, max_val)
    }

    // 分治：对值域 [low, high] 内的元素统计影子对
    fn solve(a: &[i32], low: i32, high: i32) -> i32 {
        let n = a.len();
        if n <= 1 || low == high {
            return 0;
        }

        let mid = (low + high) / 2;
        let mut low_st: Vec<usize> = Vec::new();  // 下部元素的单调栈（值递增）
        let mut high_st: Vec<usize> = Vec::new(); // 上部元素的单调栈（值递减）
        let mut b: Vec<i32> = Vec::new();         // 下部元素
        let mut c: Vec<i32> = Vec::new();         // 上部元素
        let mut res = 0_i32;

        for (i, &x) in a.iter().enumerate() {
            if x <= mid {
                // x 作为 nums[i]：弹出栈中值 < x 的元素（它们会被 x 挡住）
                while let Some(&top) = low_st.last() {
                    if a[top] < x {
                        low_st.pop();
                    } else {
                        break;
                    }
                }
                low_st.push(i);
                b.push(x);
            } else {
                // x 作为 nums[j]：维护递减栈，栈顶为左侧最近的 < x 的元素下标
                while let Some(&top) = high_st.last() {
                    if a[top] >= x {
                        high_st.pop();
                    } else {
                        break;
                    }
                }
                res += low_st.len() as i32;
                if let Some(&k) = high_st.last() {
                    // low_st 中下标 < k 的元素不能作为 nums[i]
                    let cnt = low_st.partition_point(|&idx| idx < k) as i32;
                    res -= cnt;
                }
                high_st.push(i);
                c.push(x);
            }
        }

        res + Self::solve(&b, low, mid) + Self::solve(&c, mid + 1, high)
    }
}
```
