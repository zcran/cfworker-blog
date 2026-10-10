---
title: "leetcode-数学100"
date: 2026-10-07T20:39:35+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 至多 K 个不同质因数集合的最长子数组

给你一个由正整数组成的整数数组 nums 和一个整数 k。

一个 子数组 的 质因数集合 是其所有元素的 不同质 因数的 并集。

返回 最长子数组的长度 ，其质因数集合中包含的不同质因子数量不超过 k 。如果不存在这样的子数组，则返回 0。

子数组 是数组中一段连续 非空 的元素序列。

质数 是指在大于 1 的自然数中，除了 1 和它本身以外不再有其他因数的自然数。


```
use std::collections::HashMap;
use std::sync::OnceLock;

const MX: usize = 100_001;

// 每个数的最小/全部不同质因子，使用 OnceLock 做懒加载
fn prime_factors() -> &'static Vec<Vec<usize>> {
    static PF: OnceLock<Vec<Vec<usize>>> = OnceLock::new();
    PF.get_or_init(|| {
        let mut pf: Vec<Vec<usize>> = vec![Vec::new(); MX];
        for i in 2..MX {
            if pf[i].is_empty() {
                // i 是质数，标记其所有倍数
                let mut j = i;
                while j < MX {
                    pf[j].push(i);
                    j += i;
                }
            }
        }
        pf
    })
}

impl Solution {
    pub fn longest_subarray(nums: Vec<i32>, k: i32) -> i32 {
        let pf = prime_factors();
        let k = k as usize;

        // 滑动窗口：cnt 记录窗口内各质因子的出现次数
        let mut cnt: HashMap<usize, usize> = HashMap::new();
        let mut ans = 0;
        let mut left = 0;

        for right in 0..nums.len() {
            // 加入右端元素的质因子
            for &p in &pf[nums[right] as usize] {
                *cnt.entry(p).or_insert(0) += 1;
            }

            // 收缩窗口，直到不同质因子数量不超过 k
            while cnt.len() > k {
                for &p in &pf[nums[left] as usize] {
                    if let Some(c) = cnt.get_mut(&p) {
                        *c -= 1;
                        if *c == 0 {
                            cnt.remove(&p);
                        }
                    }
                }
                left += 1;
            }

            ans = ans.max(right - left + 1);
        }

        ans as i32
    }
}
```
