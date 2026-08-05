---
title: "leetcode-枚举32"
date: 2026-07-09T10:05:00+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 个位数字为 K 的整数之和

给你两个整数 num 和 k ，考虑具有以下属性的正整数多重集：

每个整数个位数字都是 k 。
所有整数之和是 num 。
返回该多重集的最小大小，如果不存在这样的多重集，返回 -1 。

注意：

多重集与集合类似，但多重集可以包含多个同一整数，空多重集的和为 0 。
个位数字 是数字最右边的数位。



```
impl Solution {
    pub fn minimum_numbers(num: i32, k: i32) -> i32 {
        if num == 0 {
            return 0;
        }

        // 每个数的个位是 k，所以每个数 ≡ k (mod 10)
        // i 个数之和 ≡ i * k (mod 10)，必须等于 num (mod 10)
        // 最多只需要检查 10 个数，因为 (i * k) % 10 会循环
        for i in 1..=10 {
            if i * k <= num && (num - i * k) % 10 == 0 {
                return i;
            }
        }

        -1
    }
}
```
