---
title: "leetcode-数论33"
date: 2026-06-20T11:09:56+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 对角线上的质数

给你一个下标从 0 开始的二维整数数组 nums 。

返回位于 nums 至少一条 对角线 上的最大 质数 。如果任一对角线上均不存在质数，返回 0 。

注意：

如果某个整数大于 1 ，且不存在除 1 和自身之外的正整数因子，则认为该整数是一个质数。
如果存在整数 i ，使得 nums[i][i] = val 或者 nums[i][nums.length - i - 1]= val ，则认为整数 val 位于 nums 的一条对角线上。

-------------------------------------
      1   ｜     2     ｜     3
-------------------------------------
      4   ｜     5     ｜     6
-------------------------------------
      7   ｜     8     ｜     9
-------------------------------------

在上图中，一条对角线是 [1,5,9] ，而另一条对角线是 [3,5,7] 。


```
impl Solution {
    /// 返回位于矩阵至少一条对角线上的最大质数
    ///
    /// 两条对角线：
    /// - 主对角线：nums[i][i]
    /// - 副对角线：nums[i][n-1-i]
    /// 当 n 为奇数时，中心元素会被检查两次，但不影响结果
    pub fn diagonal_prime(nums: Vec<Vec<i32>>) -> i32 {
        let n = nums.len();
        let mut max_prime = 0;

        for i in 0..n {
            // 检查主对角线
            let val = nums[i][i];
            if val > max_prime && is_prime(val) {
                max_prime = val;
            }

            // 检查副对角线（避免重复检查中心元素）
            let j = n - 1 - i;
            if j != i {
                let val = nums[i][j];
                if val > max_prime && is_prime(val) {
                    max_prime = val;
                }
            }
        }

        max_prime
    }
}

/// 判断一个数是否为质数
///
/// 原理：检查从 2 到 sqrt(n) 的所有因子
/// 优化：提前排除偶数，减少循环次数
#[inline]
fn is_prime(num: i32) -> bool {
    if num < 2 {
        return false;
    }
    if num == 2 {
        return true;
    }
    if num % 2 == 0 {
        return false;
    }

    let mut factor = 3;
    while factor * factor <= num {
        if num % factor == 0 {
            return false;
        }
        factor += 2; // 只检查奇数因子
    }

    true
}
```
