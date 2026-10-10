---
title: "leetcode-数学5"
date: 2026-10-07T20:39:28+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 自除数

自除数 是指可以被它包含的每一位数整除的数。

例如，128 是一个 自除数 ，因为 128 % 1 == 0，128 % 2 == 0，128 % 8 == 0。

自除数 不允许包含 0 。

给定两个整数 left 和 right ，返回一个列表，列表的元素是范围 [left, right]（包括两个端点）内所有的 自除数 。


```
impl Solution {
    /// 返回范围 [left, right] 内所有的自除数。
    ///
    /// 自除数：能被自身每一位数字整除的数，且不含数字 0。
    /// 直接遍历区间，对每个数逐位判断即可。
    pub fn self_dividing_numbers(left: i32, right: i32) -> Vec<i32> {
        (left..=right).filter(|&n| Self::is_self_dividing(n)).collect()
    }

    /// 判断 n 是否为自除数。
    #[inline]
    fn is_self_dividing(mut n: i32) -> bool {
        let original = n;
        while n > 0 {
            let digit = n % 10;
            // 含 0 或不能被该位整除，则不是自除数
            if digit == 0 || original % digit != 0 {
                return false;
            }
            n /= 10;
        }
        true
    }
}
```
