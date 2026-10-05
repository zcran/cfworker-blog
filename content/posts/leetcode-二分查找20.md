---
title: "leetcode-二分查找20"
date: 2026-09-28T12:29:11+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 黑名单中的随机数

给定一个整数 n 和一个 无重复 黑名单整数数组 blacklist 。设计一种算法，从 [0, n - 1] 范围内的任意整数中选取一个 未加入 黑名单 blacklist 的整数。任何在上述范围内且不在黑名单 blacklist 中的整数都应该有 同等的可能性 被返回。

优化你的算法，使它最小化调用语言 内置 随机函数的次数。

实现 Solution 类:

Solution(int n, int[] blacklist) 初始化整数 n 和被加入黑名单 blacklist 的整数
int pick() 返回一个范围为 [0, n - 1] 且不在黑名单 blacklist 中的随机整数


```
use rand::{thread_rng, Rng};
use std::collections::{HashMap, HashSet};

/// 白名单长度为 n - blacklist.len()。
/// 把 [0, size) 中命中黑名单的位置，映射到 [size, n) 中不在黑名单的位置。
/// pick 时只需在 [0, size) 随机取一个数，查表替换即可，随机函数只调用一次。
struct Solution {
    size: i32,
    map: HashMap<i32, i32>,
}

impl Solution {
    fn new(n: i32, blacklist: Vec<i32>) -> Self {
        let size = n - blacklist.len() as i32;
        let black: HashSet<i32> = blacklist.iter().copied().collect();

        // 收集 [size, n) 中所有不在黑名单里的数，用于替换
        let mut candidates = (size..n).filter(|x| !black.contains(x));

        let mut map = HashMap::new();
        for &b in &blacklist {
            // 只处理落在 [0, size) 中的黑名单元素
            if b < size {
                map.insert(b, candidates.next().unwrap());
            }
        }

        Solution { size, map }
    }

    fn pick(&self) -> i32 {
        let x = thread_rng().gen_range(0..self.size);
        *self.map.get(&x).unwrap_or(&x)
    }
}

/**
 * Your Solution object will be instantiated and called as such:
 * let obj = Solution::new(n, blacklist);
 * let ret_1: i32 = obj.pick();
 */
```
