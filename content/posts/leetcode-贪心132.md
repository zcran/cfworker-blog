---
title: "leetcode-贪心132"
date: 2026-10-03T12:55:43+08:00
tags: ["leetcode", "贪心"]
draft: false
---


## 需要添加的硬币的最小数量

给你一个下标从 0 开始的整数数组 coins，表示可用的硬币的面值，以及一个整数 target 。

如果存在某个 coins 的子序列总和为 x，那么整数 x 就是一个 可取得的金额 。

返回需要添加到数组中的 任意面值 硬币的 最小数量 ，使范围 [1, target] 内的每个整数都属于 可取得的金额 。

数组的 子序列 是通过删除原始数组的一些（可能不删除）元素而形成的新的 非空 数组，删除过程不会改变剩余元素的相对位置。


```
impl Solution {
    pub fn minimum_added_coins(mut coins: Vec<i32>, target: i32) -> i32 {
        // 贪心：维护一个可达区间 [0, s-1]（初始 s = 1，仅 {0} 可达）。
        // 若当前硬币 coins[i] <= s，则可达范围扩展为 [0, s + coins[i] - 1]；
        // 否则说明 s 这个金额无法凑出，必须补一枚面值 s 的硬币，
        // 直接把可达范围翻倍为 [0, 2s - 1]。
        coins.sort_unstable();

        let mut added = 0;
        let mut s = 1i64; // 下一个待覆盖的金额
        let mut i = 0;

        while s <= target as i64 {
            if i < coins.len() && coins[i] as i64 <= s {
                s += coins[i] as i64;
                i += 1;
            } else {
                s *= 2;
                added += 1;
            }
        }
        added
    }
}
```
