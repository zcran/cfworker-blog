---
title: "leetcode-位运算89"
date: 2026-09-24T10:33:40+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 撞色搭配

整数数组 sockets 记录了一个袜子礼盒的颜色分布情况，其中 sockets[i] 表示该袜子的颜色编号。礼盒中除了一款撞色搭配的袜子，每种颜色的袜子均有两只。请设计一个程序，在时间复杂度 O(n)，空间复杂度O(1) 内找到这双撞色搭配袜子的两个颜色编号。


```
impl Solution {
    pub fn sock_collocation(nums: Vec<i32>) -> Vec<i32> {
        // 思路：只有一个数出现一次、其余数都出现两次（异或抵消）。
        // 1. 全部异或，得到两个只出现一次的数 a ^ b。
        // 2. 取 d = (a^b) 的最低位 1，用来把数组分成两组：
        //    a 和 b 必然分属不同组，各自组内异或即可得到 a、b。
        let xor_all = nums.iter().fold(0, |acc, &x| acc ^ x);
        let d = xor_all & xor_all.wrapping_neg(); // 最低位的 1

        // 按 d 位分组异或
        let mut a = 0;
        let mut b = 0;
        for &x in &nums {
            if x & d == 0 {
                a ^= x;
            } else {
                b ^= x;
            }
        }

        vec![a, b]
    }
}
```
