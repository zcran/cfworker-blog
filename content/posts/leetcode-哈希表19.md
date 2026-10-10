---
title: "leetcode-哈希表19"
date: 2026-10-09T20:07:56+08:00
tags: ["leetcode", "哈希表"]
draft: false
---


## 宝石与石头

 给你一个字符串 jewels 代表石头中宝石的类型，另有一个字符串 stones 代表你拥有的石头。 stones 中每个字符代表了一种你拥有的石头的类型，你想知道你拥有的石头中有多少是宝石。

字母区分大小写，因此 "a" 和 "A" 是不同类型的石头。


```
impl Solution {
    pub fn num_jewels_in_stones(jewels: String, stones: String) -> i32 {
        // 用 128 大小的布尔表标记宝石字符（ASCII），查表 O(1)。
        // 单次遍历 stones，避免 O(j*s) 双重循环。
        let mut is_jewel = [false; 128];
        for b in jewels.bytes() {
            is_jewel[b as usize] = true;
        }
        stones.bytes().filter(|&b| is_jewel[b as usize]).count() as i32
    }
}
```
