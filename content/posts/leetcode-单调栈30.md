---
title: "leetcode-单调栈30"
date: 2026-06-07T10:14:05+08:00
tags: ["leetcode", "单调栈"]
draft: false
---

## 子数组最小乘积的最大值

一个数组的 最小乘积 定义为这个数组中 最小值 乘以 数组的 和 。

比方说，数组 [3,2,5] （最小值是 2）的最小乘积为 2 * (3+2+5) = 2 * 10 = 20 。
给你一个正整数数组 nums ，请你返回 nums 任意 非空子数组 的最小乘积 的 最大值 。由于答案可能很大，请你返回答案对  10^9 + 7 取余 的结果。

请注意，最小乘积的最大值考虑的是取余操作 之前 的结果。题目保证最小乘积的最大值在 不取余 的情况下可以用 64 位有符号整数 保存。

子数组 定义为一个数组的 连续 部分。


```
impl Solution {
    /// 最大子数组最小乘积
    ///
    /// 单调栈 + 前缀和
    ///
    /// # 思路
    /// 1. 每个元素作为最小值时，区间越大乘积可能越大
    /// 2. 用单调栈找到左右第一个比它小的元素
    /// 3. 用前缀和快速计算区间和
    ///
    /// # 复杂度
    /// - 时间: O(n)
    /// - 空间: O(n)
    pub fn max_sum_min_product(nums: Vec<i32>) -> i32 {
        let n = nums.len();
        const MOD: i64 = 1_000_000_007;

        // 前缀和，prefix[i] 表示前 i 个元素的和
        let mut prefix = vec![0i64; n + 1];
        for (i, &x) in nums.iter().enumerate() {
            prefix[i + 1] = prefix[i] + x as i64;
        }

        // 单调栈，存储索引，栈内元素值严格递增
        let mut stack: Vec<usize> = Vec::with_capacity(n);
        let mut left = vec![0; n];   // 左边界（第一个小于当前值的位置+1）
        let mut right = vec![n - 1; n]; // 右边界（第一个小于当前值的位置-1）

        for i in 0..n {
            // 弹出所有 >= 当前值的栈顶元素
            // 此时当前值是这些元素的右边界
            while let Some(&top) = stack.last() && nums[top] >= nums[i] {
                right[top] = i - 1;
                stack.pop();
            }
            // 栈顶元素是左边第一个 < 当前值的元素
            left[i] = stack.last().map_or(0, |&pos| pos + 1);
            stack.push(i);
        }

        // 栈中剩余的元素，右边没有更小的，右边界为 n-1
        // left 数组已在循环中确定，无需额外处理

        // 计算最大乘积
        let mut ans = 0;
        for i in 0..n {
            let range_sum = prefix[right[i] + 1] - prefix[left[i]];
            let product = nums[i] as i64 * range_sum;
            if product > ans {
                ans = product;
            }
        }

        (ans % MOD) as i32
    }
}
```
