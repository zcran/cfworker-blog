---
title: "leetcode-滑动窗口26"
date: 2026-07-18T11:02:30+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 爱生气的书店老板

有一个书店老板，他的书店开了 n 分钟。每分钟都有一些顾客进入这家商店。给定一个长度为 n 的整数数组 customers ，其中 customers[i] 是在第 i 分钟开始时进入商店的顾客数量，所有这些顾客在第 i 分钟结束后离开。

在某些分钟内，书店老板会生气。 如果书店老板在第 i 分钟生气，那么 grumpy[i] = 1，否则 grumpy[i] = 0。

当书店老板生气时，那一分钟的顾客就会不满意，若老板不生气则顾客是满意的。

书店老板知道一个秘密技巧，能抑制自己的情绪，可以让自己连续 minutes 分钟不生气，但却只能使用一次。

请你返回 这一天营业下来，最多有多少客户能够感到满意 。


```
impl Solution {
    pub fn max_satisfied(customers: Vec<i32>, grumpy: Vec<i32>, minutes: i32) -> i32 {
        let k = minutes as usize;
        let n = customers.len();

        // 统计本来就不生气时已经满意的顾客总数
        let mut base_satisfied = 0;
        for i in 0..n {
            if grumpy[i] == 0 {
                base_satisfied += customers[i];
            }
        }

        // 滑动窗口计算使用技巧后能额外挽回的最大不满意顾客数
        let mut extra = 0;
        let mut max_extra = 0;

        for i in 0..n {
            // 窗口内的顾客，如果老板本来生气，则会被挽回
            if grumpy[i] == 1 {
                extra += customers[i];
            }

            // 窗口大小达到 minutes，更新最大值
            if i >= k - 1 {
                max_extra = max_extra.max(extra);

                // 滑动窗口：移除最左边的元素
                if grumpy[i - k + 1] == 1 {
                    extra -= customers[i - k + 1];
                }
            }
        }

        base_satisfied + max_extra
    }
}
```
