---
title: "leetcode-组合数学31"
date: 2026-05-24T09:54:13+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 查询排序后的最大公约数

给你一个长度为 n 的整数数组 nums 和一个整数数组 queries 。

gcdPairs 表示数组 nums 中所有满足 0 <= i < j < n 的数对 (nums[i], nums[j]) 的 最大公约数 升序 排列构成的数组。

对于每个查询 queries[i] ，你需要找到 gcdPairs 中下标为 queries[i] 的元素。

请你返回一个整数数组 answer ，其中 answer[i] 是 gcdPairs[queries[i]] 的值。

gcd(a, b) 表示 a 和 b 的 最大公约数 。


```
impl Solution {
    pub fn gcd_values(nums: Vec<i32>, queries: Vec<i64>) -> Vec<i32> {
        // 处理空输入
        if nums.is_empty() {
            return vec![];
        }

        // 1. 统计每个数值的出现次数，并确定最大值
        let max_val = *nums.iter().max().unwrap() as usize;
        let mut cnt = vec![0i64; max_val + 1];
        for &num in &nums {
            cnt[num as usize] += 1;
        }

        // 2. 线性筛法计算莫比乌斯函数 μ(x)
        let mut mu = vec![1i64; max_val + 1];
        let mut primes = Vec::new();
        let mut is_comp = vec![false; max_val + 1];
        for i in 2..=max_val {
            if !is_comp[i] {
                primes.push(i);
                mu[i] = -1;               // 质数的 μ = -1
            }
            for &p in &primes {
                let n = i * p;
                if n > max_val {
                    break;
                }
                is_comp[n] = true;
                if i % p == 0 {
                    mu[n] = 0;            // 含有平方因子，μ = 0
                    break;
                } else {
                    mu[n] = -mu[i];       // 新质因子，符号反转
                }
            }
        }

        // 3. 计算每个 d 的倍数对个数（无序对）
        // 复用 cnt 数组，就地计算倍数计数：multiples[d] = sum_{d|k} cnt[k]
        let mut multiples = vec![0i64; max_val + 1];
        for d in 1..=max_val {
            let mut sum = 0;
            for k in (d..=max_val).step_by(d) {
                sum += cnt[k];
            }
            multiples[d] = sum * (sum - 1) / 2; // C(sum,2)
        }

        // 4. 容斥：利用莫比乌斯函数计算“恰好等于 d” 的数对个数
        // 直接原地更新 multiples 数组为 exact[d]
        for d in 1..=max_val {
            let mut exact = 0;
            for k in (d..=max_val).step_by(d) {
                exact += mu[k / d] * multiples[k];
            }
            multiples[d] = exact; // 现在存储的是 gcd 恰好为 d 的对数
        }

        // 5. 计算前缀和：prefix[d] = gcd 值 ≤ d 的总对数
        for d in 2..=max_val {
            multiples[d] += multiples[d - 1];
        }

        // 6. 回答查询：二分查找最小 gcd 值使得前缀和 > query
        queries
            .iter()
            .map(|&q| {
                // multiples 是单调递增的，找到第一个 > q 的位置
                (multiples.partition_point(|&x| x <= q) as i32)
            })
            .collect()
    }
}
```
