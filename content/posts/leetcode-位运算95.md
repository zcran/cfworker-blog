---
title: "leetcode-位运算95"
date: 2026-09-24T10:33:40+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 翻转数位

给定一个32位整数 num，你可以将一个数位从0变为1。请编写一个程序，找出你能够获得的最长的一串1的长度。

```
impl Solution {
    pub fn reverse_bits(num: i32) -> i32 {
        // 目标：把一个 0 变成 1 后，能得到的最长连续 1 的长度。
        //
        // 位运算扫描：用 prev 记录上一段连续 1 的长度，
        // cur 记录当前段连续 1 的长度。遇到 0 时结算一次，
        // 用 prev + 1 + cur 更新答案（把该 0 翻成 1 后连接左右两段）。
        //
        // 特例：若 num 全为 1（如 -1），没有 0 可翻转，最长连续 1 即为 32。

        // 全 1 特判：翻转后仍是 32 位全 1
        if num == -1 {
            return 32;
        }

        let mut ans = 1;  // 至少能翻转出一个 1
        let mut prev = 0; // 上一段连续 1 的长度
        let mut cur = 0;  // 当前段连续 1 的长度

        for i in 0..32 {
            if (num >> i) & 1 == 1 {
                cur += 1;
            } else {
                // 遇到 0：用 prev + 1 + cur 尝试更新答案（这个 0 翻成 1）
                ans = ans.max(prev + 1 + cur);
                prev = cur;
                cur = 0;
            }
        }
        // 处理末尾没有 0 结算的情况
        ans.max(prev + 1 + cur)
    }
}
```
