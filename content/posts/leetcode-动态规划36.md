---
title: "leetcode-动态规划36"
date: 2026-10-05T20:39:56+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 两个键的键盘

最初记事本上只有一个字符 'A' 。你每次可以对这个记事本进行两种操作：

Copy All（复制全部）：复制这个记事本中的所有字符（不允许仅复制部分字符）。
Paste（粘贴）：粘贴 上一次 复制的字符。

给你一个数字 n ，你需要使用最少的操作次数，在记事本上输出 恰好 n 个 'A' 。返回能够打印出 n 个 'A' 的最少操作次数。


```
impl Solution {
    pub fn min_steps(n: i32) -> i32 {
        // 本质是求 n 的质因数之和（贪心：每次以最小质因子为"复制单位"）
        let mut ans = 0;
        let mut n = n;

        // 只需试除到 sqrt(n)
        let mut i = 2;
        while i * i <= n {
            while n % i == 0 {
                ans += i;
                n /= i;
            }
            i += 1;
        }

        // 剩余部分本身是质数，直接累加
        if n > 1 {
            ans += n;
        }

        ans
    }
}
```
