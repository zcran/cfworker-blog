---
title: "leetcode-分治19"
date: 2026-06-13T10:51:38+08:00
tags: ["leetcode", "分治"]
draft: false
---


## 漂亮数组

如果长度为 n 的数组 nums 满足下述条件，则认为该数组是一个 漂亮数组 ：

nums 是由范围 [1, n] 的整数组成的一个排列。

对于每个 0 <= i < j < n ，均不存在下标 k（i < k < j）使得 2 * nums[k] == nums[i] + nums[j] 。

给你整数 n ，返回长度为 n 的任一 漂亮数组 。本题保证对于给定的 n 至少存在一个有效答案。

```
use std::collections::HashMap;

impl Solution {
    /// 构造漂亮数组
    ///
    /// 算法思路（分治 + 递归）：
    /// 1. 如果 N == 1，返回 [1]
    /// 2. 否则：
    ///    - 递归构造奇数部分：f((N+1)/2) 映射为 2*x - 1（所有奇数）
    ///    - 递归构造偶数部分：f(N/2) 映射为 2*x（所有偶数）
    ///    - 合并两部分
    ///
    /// 数学原理：2*(2*x-1) != (2*a-1) + (2*b-1) 且 2*(2*x) != (2*a) + (2*b)
    /// 即奇偶分离可以保证漂亮数组性质
    pub fn beautiful_array(n: i32) -> Vec<i32> {
        let n = n as usize;
        let mut memo = HashMap::new();

        // 递归函数（使用闭包实现记忆化）
        fn f(n: usize, memo: &mut HashMap<usize, Vec<i32>>) -> Vec<i32> {
            if let Some(result) = memo.get(&n) {
                return result.clone();
            }

            let ans = if n == 1 {
                vec![1]
            } else {
                let mut result = Vec::with_capacity(n);

                // 奇数部分：2*x - 1
                for &x in &f((n + 1) / 2, memo) {
                    result.push(2 * x - 1);
                }

                // 偶数部分：2*x
                for &x in &f(n / 2, memo) {
                    result.push(2 * x);
                }

                result
            };

            memo.insert(n, ans.clone());
            ans
        }

        f(n, &mut memo)
    }
}
```
