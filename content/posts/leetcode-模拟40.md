---
title: "leetcode-模拟40"
date: 2026-08-08T11:31:10+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 连接连续二进制数字

给你一个整数 n ，请你将 1 到 n 的二进制表示连接起来，并返回连接结果对应的 十进制 数字对 10^9 + 7 取余的结果。


```
impl Solution {
    pub fn concatenated_binary(n: i32) -> i32 {
            const MOD: i64 = 1_000_000_007;
            let mut ans: i64 = 0;
            let mut shift: i64 = 0; // 当前已累积的二进制位数
        for i in 1..=n as i64 {   // 当 i 是 2 的幂时，二进制位数增加 1
                                  // 例如 i=1(1b), 2(10b), 4(100b), 8(1000b)...
            if i & (i - 1) == 0 {
            shift += 1;
        }
        // 将 ans 左移 shift 位，然后拼接 i
        ans = ((ans << shift) | i) % MOD;
    }
      ans as i32
  }
}
```
