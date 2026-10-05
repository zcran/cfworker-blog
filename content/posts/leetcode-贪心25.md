---
title: "leetcode-贪心25"
date: 2026-10-03T12:55:35+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 柠檬水找零

在柠檬水摊上，每一杯柠檬水的售价为 5 美元。顾客排队购买你的产品，（按账单 bills 支付的顺序）一次购买一杯。

每位顾客只买一杯柠檬水，然后向你付 5 美元、10 美元或 20 美元。你必须给每个顾客正确找零，也就是说净交易是每位顾客向你支付 5 美元。

注意，一开始你手头没有任何零钱。

给你一个整数数组 bills ，其中 bills[i] 是第 i 位顾客付的账。如果你能给每位顾客正确找零，返回 true ，否则返回 false 。


```
impl Solution {
    pub fn lemonade_change(bills: Vec<i32>) -> bool {
        // five / ten: 手头 5 美元、10 美元钞票的数量
        // 20 美元无法用于找零，因此无需记录
        let (mut five, mut ten) = (0, 0);

        for b in bills {
            match b {
                5 => five += 1,
                10 => {
                    // 收 10 找 5
                    five -= 1;
                    ten += 1;
                }
                _ => {
                    // 收 20 优先找 10 + 5（保留 5 美元更灵活），否则找 5 + 5 + 5
                    if ten > 0 {
                        ten -= 1;
                        five -= 1;
                    } else {
                        five -= 3;
                    }
                }
            }

            // 5 美元不足，无法完成找零
            if five < 0 {
                return false;
            }
        }

        true
    }
}
```
