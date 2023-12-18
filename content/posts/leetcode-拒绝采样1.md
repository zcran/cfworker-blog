---
title: "Leetcode 拒绝采样1"
date: 2023-06-12T10:56:45+08:00
tags: ["leetcode", "拒绝采样"]
draft: false
---

## 用rand7()实现rand10()

给定方法 rand7 可生成 [1,7] 范围内的均匀随机整数，试写一个方法 rand10 生成 [1,10] 范围内的均匀随机整数。

你只能调用 rand7() 且不能调用其他方法。请不要使用系统的 Math.random() 方法。

每个测试用例将有一个内部参数 n，即你实现的函数 rand10() 在测试时将被调用的次数。请注意，这不是传递给 rand10() 的参数。


```
impl Solution {
    pub fn rand10() -> i32 {
        let mut x = std::i32::MAX;
        while x > 40 {
            x = 7 * (rand7() -1) + rand7();
        }   
        return x%10+1;     
    }
}
```