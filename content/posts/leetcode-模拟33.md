---
title: "leetcode-模拟33"
date: 2026-08-08T11:31:10+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 换水问题

超市正在促销，你可以用 numExchange 个空水瓶从超市兑换一瓶水。最开始，你一共购入了 numBottles 瓶水。

如果喝掉了水瓶中的水，那么水瓶就会变成空的。

给你两个整数 numBottles 和 numExchange ，返回你 最多 可以喝到多少瓶水。


```
impl Solution {
    pub fn num_water_bottles(num_bottles: i32, num_exchange: i32) -> i32 {
        // 每 num_exchange 个空瓶换 1 瓶水，喝完后又得到 1 个空瓶
        // 等价于每轮净消耗 (num_exchange - 1) 个空瓶
        // 初始 n 瓶喝完后产生 n 个空瓶，之后每 (k-1) 个空瓶可多换 1 瓶
        // 总瓶数 = 初始瓶数 + 额外兑换瓶数
        num_bottles + if num_bottles >= num_exchange {
            (num_bottles - 1) / (num_exchange - 1)
        } else {
            0
        }
    }
}
```
