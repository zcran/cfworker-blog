---
title: "leetcode-递归44"
date: 2026-05-26T10:00:18+08:00
tags: ["leetcode", "递归"]
draft: false
---


## 破冰游戏

社团共有 num 位成员参与破冰游戏，编号为 0 ~ num-1。成员们按照编号顺序围绕圆桌而坐。社长抽取一个数字 target，从 0 号成员起开始计数，排在第 target 位的成员离开圆桌，且成员离开后从下一个成员开始计数。请返回游戏结束时最后一位成员的编号。


```
impl Solution {
    // 约瑟夫环问题 - 题目要求的函数名
    pub fn ice_breaking_game(n: i32, m: i32) -> i32 {
        let mut ans = 0;
        // 从2个人开始递推到n个人
        (2..=n).for_each(|x| ans = (ans + m) % x);
        ans
    }
}
```
