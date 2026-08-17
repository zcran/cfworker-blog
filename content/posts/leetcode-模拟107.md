---
title: "leetcode-模拟107"
date: 2026-08-08T11:31:15+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 换水问题 II

给你两个整数 numBottles 和 numExchange 。

numBottles 代表你最初拥有的满水瓶数量。在一次操作中，你可以执行以下操作之一：

喝掉任意数量的满水瓶，使它们变成空水瓶。

用 numExchange 个空水瓶交换一个满水瓶。然后，将 numExchange 的值增加 1 。

注意，你不能使用相同的 numExchange 值交换多批空水瓶。例如，如果 numBottles == 3 并且 numExchange == 1 ，则不能用 3 个空水瓶交换成 3 个满水瓶。

返回你 最多 可以喝到多少瓶水。


```
impl Solution {
    pub fn max_bottles_drunk(num_bottles: i32, mut num_exchange: i32) -> i32 {
        let mut total = num_bottles;  // 总共喝到的瓶数（先全部喝掉初始的）
        let mut empty = num_bottles;  // 当前空瓶数量

        // 每次用 numExchange 个空瓶换 1 个满瓶，喝掉后空瓶净减 (numExchange - 1)
        while empty >= num_exchange {
            total += 1;
            empty -= num_exchange - 1;  // 花 numExchange 个空瓶，换 1 个满瓶喝掉变空瓶
            num_exchange += 1;
        }

        total
    }
}
```
