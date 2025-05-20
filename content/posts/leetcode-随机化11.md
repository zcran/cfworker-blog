---
title: "Leetcode 随机化11"
date: 2024-12-21T15:03:43+08:00
tags: ["leetcode", "随机化"]
draft: false
---

##  黑名单中的随机数
给定一个整数 n 和一个 无重复 黑名单整数数组 blacklist 。设计一种算法，从 [0, n - 1] 范围内的任意整数中选取一个 未加入 黑名单 blacklist 的整数。任何在上述范围内且不在黑名单 blacklist 中的整数都应该有 同等的可能性 被返回。

优化你的算法，使它最小化调用语言 内置 随机函数的次数。

实现 Solution 类:

Solution(int n, int[] blacklist) 初始化整数 n 和被加入黑名单 blacklist 的整数
int pick() 返回一个范围为 [0, n - 1] 且不在黑名单 blacklist 中的随机整数


```
use std::collections::HashMap;
use rand::Rng;

pub struct Solution {
    map: HashMap<i32, i32>,
    size: i32,
}

#[allow(dead_code)]
impl Solution {
    pub fn new(n: i32, blacklist: Vec<i32>) -> Self {
        let (mut length, mut n) = (blacklist.len() as i32, n);

        let mut blacklist = blacklist;
        blacklist.sort();

        let mut map: HashMap<i32, i32> = HashMap::new();
        let mut i: i32 = 0;
        while i < length && blacklist[i as usize] < n {
            n -= 1;
            while n > blacklist[i as usize] {
                if n == blacklist[(length - 1) as usize] {
                    length -= 1;
                } else {
                    map.insert(blacklist[i as usize], n);
                    break;
                }
                n -= 1;
            }
            i += 1;
        }

        Self { map, size: n }

    }

    pub fn pick(&self) -> i32 {
        let ans = rand::thread_rng().gen_range(0..self.size) as i32;

        if let Some(x) = self.map.get(&ans) {
            return *x;
        }

        ans
    }
}

```