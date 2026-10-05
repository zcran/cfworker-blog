---
title: "leetcode-二分查找23"
date: 2026-09-28T12:29:11+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 阶乘函数后 K 个零

 f(x) 是 x! 末尾是 0 的数量。回想一下 x! = 1 * 2 * 3 * ... * x，且 0! = 1 。

例如， f(3) = 0 ，因为 3! = 6 的末尾没有 0 ；而 f(11) = 2 ，因为 11!= 39916800 末端有 2 个 0 。

给定 k，找出返回能满足 f(x) = k 的非负整数 x 的数量。


```
impl Solution {
    /// 返回满足 f(x) == k 的非负整数 x 的个数。
    ///
    /// f(x) 为 x! 末尾零的个数，等于 x! 中因子 5 的个数：
    ///     f(x) = x/5 + x/25 + x/125 + ...
    /// f(x) 单调非递减，且每次跳跃步长可能 >1，因此满足 f(x)==k 的 x
    /// 要么是 0 个，要么恰好是 5 个连续整数。
    ///
    /// 用二分找出第一个使 f(x) >= k 的 x（下界 L），
    /// 以及第一个使 f(x) >= k+1 的 x（下界 R），
    /// 则个数为 R - L。
    pub fn preimage_size_fzf(k: i32) -> i32 {
        // 计算 n! 末尾零的个数
        fn zeta(mut n: i64) -> i64 {
            let mut res = 0;
            while n > 0 {
                n /= 5;
                res += n;
            }
            res
        }

        // 二分查找第一个使 zeta(x) >= k 的 x
        fn lower_bound(k: i64) -> i64 {
            let (mut lo, mut hi) = (0, k * 5);
            while lo < hi {
                let mid = lo + (hi - lo) / 2;
                if zeta(mid) < k {
                    lo = mid + 1;
                } else {
                    hi = mid;
                }
            }
            lo
        }

        let k = k as i64;
        (lower_bound(k + 1) - lower_bound(k)) as i32
    }
}
```
