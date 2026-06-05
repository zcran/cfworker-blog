---
title: "leetcode-组合数学41"
date: 2026-05-24T09:54:14+08:00
tags: ["leetcode", "组合数学"]
draft: false
---


## 全排列 IV


给你两个整数 n 和 k，一个 交替排列 是前 n 个正整数的排列，且任意相邻 两个 元素不都为奇数或都为偶数。

返回第 k 个 交替排列 ，并按 字典序 排序。如果有效的 交替排列 少于 k 个，则返回一个空列表。


```
use std::collections::VecDeque;

lazy_static::lazy_static! {
    static ref FACTORIALS: Vec<u128> = {
        let mut facts = vec![1; 1000];
        for i in 2..facts.len() {
            facts[i] = facts[i - 1] * i as u128;
        }
        facts
    };
}

impl Solution {
    pub fn permute(n: i32, k: i64) -> Vec<i32> {
        let n = n as usize;
        let k = k as u128;

        // Initialize odd and even number groups
        let mut odds: VecDeque<i32> = (1..=n as i32).step_by(2).collect();
        let mut evens: VecDeque<i32> = (2..=n as i32).step_by(2).collect();

        let odd_count = odds.len();
        let even_count = evens.len();

        // Calculate total valid permutations
        let total_permutations = if n % 2 == 1 {
            FACTORIALS[odd_count] * FACTORIALS[even_count]
        } else {
            FACTORIALS[odd_count] * FACTORIALS[even_count] * 2
        };

        if k == 0 || k > total_permutations {
            return vec![];
        }

        let mut result = Vec::with_capacity(n);
        let mut remaining_k = k;

        // Select first number
        let first_number = if n % 2 == 1 {
            // Must start with odd when n is odd
            let permutations = FACTORIALS[odd_count - 1] * FACTORIALS[even_count];
            let index = ((remaining_k + permutations - 1) / permutations - 1) as usize;
            let selected = odds[index];
            odds.remove(index);
            remaining_k -= permutations * index as u128;
            selected
        } else {
            // Can start with either odd or even when n is even
            let all_numbers: Vec<i32> = (1..=n as i32).collect();
            let permutations = FACTORIALS[odd_count - 1] * FACTORIALS[even_count];
            let index = ((remaining_k + permutations - 1) / permutations - 1) as usize;
            let selected = all_numbers[index];

            if selected % 2 == 1 {
                odds.remove(odds.iter().position(|&v| v == selected).unwrap());
            } else {
                evens.remove(evens.iter().position(|&v| v == selected).unwrap());
            }

            remaining_k -= permutations * index as u128;
            selected
        };
        result.push(first_number);

        // Select remaining numbers
        while remaining_k > 0 && result.len() < n {
            let last_num = result.last().unwrap();
            if last_num % 2 == 1 {
                // Last was odd, now need even
                let permutations = FACTORIALS[odds.len()] * FACTORIALS[evens.len() - 1];
                let index = ((remaining_k + permutations - 1) / permutations - 1) as usize;
                let selected = evens[index];
                evens.remove(index);
                result.push(selected);
                remaining_k -= permutations * index as u128;
            } else {
                // Last was even, now need odd
                let permutations = FACTORIALS[odds.len() - 1] * FACTORIALS[evens.len()];
                let index = ((remaining_k + permutations - 1) / permutations - 1) as usize;
                let selected = odds[index];
                odds.remove(index);
                result.push(selected);
                remaining_k -= permutations * index as u128;
            }
        }

        result
    }
}

```
