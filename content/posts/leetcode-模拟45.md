---
title: "leetcode-模拟45"
date: 2026-08-08T11:31:11+08:00
tags: ["leetcode", "模拟"]
draft: false
---


## 还原排列的最少操作步数

给你一个偶数 n​​​​​​ ，已知存在一个长度为 n 的排列 perm ，其中 perm[i] == i​（下标 从 0 开始 计数）。

一步操作中，你将创建一个新数组 arr ，对于每个 i ：

· 如果 i % 2 == 0 ，那么 arr[i] = perm[i / 2]
· 如果 i % 2 == 1 ，那么 arr[i] = perm[n / 2 + (i - 1) / 2]

然后将 arr​​ 赋值​​给 perm 。

要想使 perm 回到排列初始值，至少需要执行多少步操作？返回最小的 非零 操作步数。


```
impl Solution {
    pub fn reinitialize_permutation(n: i32) -> i32 {
        // 求 2 在模 (n-1) 下的乘法阶，即最小 k 使得 2^k ≡ 1 (mod n-1)
        if n == 2 { return 1; }

        let mut steps = 1;
        let mut val = 2;

        while val != 1 {
            steps += 1;
            val = val * 2 % (n - 1);
        }

        steps
    }
}
```
