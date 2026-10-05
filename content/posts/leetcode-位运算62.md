---
title: "leetcode-位运算62"
date: 2026-09-24T10:33:38+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 数组最后一个元素的最小值

给你两个整数 n 和 x 。你需要构造一个长度为 n 的 正整数 数组 nums ，对于所有 0 <= i < n - 1 ，满足 nums[i + 1] 大于 nums[i] ，并且数组 nums 中所有元素的按位 AND 运算结果为 x 。

返回 nums[n - 1] 可能的 最小 值。


```
impl Solution {
    pub fn min_end(n: i32, x: i32) -> i64 {
        // 结果至少为 x，然后将其二进制中为 0 的位用来"填充" n-1 的各位
        let mut res = x as i64;
        let mut n = (n - 1) as i64;

        // 逐位扫描 res 中为 0 的位置，把 n 的二进制位依次填入
        let mut i = 0;
        while n != 0 {
            if (res >> i) & 1 == 0 {
                res |= (n & 1) << i;
                n >>= 1;
            }
            i += 1;
        }

        res
    }
}
```
