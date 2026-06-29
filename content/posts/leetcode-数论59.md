---
title: "leetcode-数论59"
date: 2026-06-20T11:09:57+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 奇数和与偶数和的最大公约数

给你一个整数 n。请你计算以下两个值的 最大公约数（GCD）：

sumOdd：最小的 n 个正奇数的总和。

sumEven：最小的 n 个正偶数的总和。

返回 sumOdd 和 sumEven 的 GCD。


```
impl Solution {
    pub fn gcd_of_odd_even_sums(n: i32) -> i32 {
        n
    }
}
```

我们计算的是公差为 2 的等差数列之和，根据等差数列求和公式，我们有

sumOdd = 1 + 3 + ... + (2n - 1) = ((1 + 2n - 1)n)/2 = n^2

以及

sumEven = 2 + 4 + ... + 2n = ((2 + 2n)n)/2 = n(n + 1)

提取公约数 n 后，sumOdd 剩下 n，sumEven 剩下 n+1，这两个数一定互质。反证法：如果 n 和 n+1 不互质，那么这两个数都是某个大于 1 的整数 x 的倍数。由于 n ≠ n+1，两个不同的 x 的倍数至少相差 x，而 x>1，这与两数相差 1 矛盾，所以 n 和 n+1 一定互质。

所以最大公约数为 n。
