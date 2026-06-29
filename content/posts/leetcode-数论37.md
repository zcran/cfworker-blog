---
title: "leetcode-数论37"
date: 2026-06-20T11:09:56+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 和等于目标值的质数对

给你一个整数 n 。如果两个整数 x 和 y 满足下述条件，则认为二者形成一个质数对：

· 1 <= x <= y <= n
· x + y == n
· x 和 y 都是质数

请你以二维有序列表的形式返回符合题目要求的所有 [xi, yi] ，列表需要按 xi 的 非递减顺序 排序。如果不存在符合要求的质数对，则返回一个空数组。

注意：质数是大于 1 的自然数，并且只有两个因子，即它本身和 1 。


```
impl Solution {
    /// 找出所有和为 n 的质数对 [x, y]，其中 1 <= x <= y <= n
    ///
    /// 核心优化：
    /// 1. 偶数 n：只需检查 <= n/2 的质数 x，y = n - x 是否为质数
    /// 2. 奇数 n：唯一可能是 (2, n-2)，因为两个质数相加为奇数 => 一奇一偶 => 只能是 2
    pub fn find_prime_pairs(n: i32) -> Vec<Vec<i32>> {
        if n < 4 {
            return vec![];
        }

        let n = n as usize;
        let mut is_prime = vec![true; n + 1];
        is_prime[0] = false;
        is_prime[1] = false;

        // 埃氏筛：标记所有合数
        let limit = (n as f64).sqrt() as usize;
        for i in 2..=limit {
            if is_prime[i] {
                let mut j = i * i;
                while j <= n {
                    is_prime[j] = false;
                    j += i;
                }
            }
        }

        let mut ans = Vec::new();

        // 奇数情况：唯一可能的质数对是 (2, n-2)
        if n % 2 == 1 {
            if n > 4 && is_prime[n - 2] {
                ans.push(vec![2, (n - 2) as i32]);
            }
            return ans;
        }

        // 偶数情况：遍历所有可能的 x
        // 只需要检查 2..=n/2，且 x 为质数
        for x in 2..=n / 2 {
            if is_prime[x] && is_prime[n - x] {
                ans.push(vec![x as i32, (n - x) as i32]);
            }
        }

        ans
    }
}
```
