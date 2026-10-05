---
title: "leetcode-位运算1"
date: 2026-09-24T10:33:34+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 两数相除

给你两个整数，被除数 dividend 和除数 divisor。将两数相除，要求 不使用 乘法、除法和取余运算。

整数除法应该向零截断，也就是截去（truncate）其小数部分。例如，8.345 将被截断为 8 ，-2.7335 将被截断至 -2 。

返回被除数 dividend 除以除数 divisor 得到的 商 。

注意：假设我们的环境只能存储 32 位 有符号整数，其数值范围是 [−2^31,  2^31 − 1] 。本题中，如果商 严格大于 2^31 − 1 ，则返回 2^31 − 1 ；如果商 严格小于 -2^31 ，则返回 -2^31 。


```
impl Solution {
    pub fn divide(dividend: i32, divisor: i32) -> i32 {
        // 唯一会溢出的情况：i32::MIN / -1
        if dividend == i32::MIN && divisor == -1 {
            return i32::MAX;
        }

        // 统一转为负数处理，避免 i32::MIN 取绝对值溢出
        let mut m = if dividend > 0 { -(dividend as i64) } else { dividend as i64 };
        let mut n = if divisor > 0 { -(divisor as i64) } else { divisor as i64 };

        let mut res: i64 = 0;

        // 两者同号（均为负）时，结果为正；否则为负
        let negative = (dividend < 0) ^ (divisor < 0);

        // 不断用倍增法逼近
        while m <= n {
            let mut t = n;
            let mut p: i64 = -1;

            // t 每次翻倍（负数左移等价于乘 2），p 同步翻倍
            while t >= (i64::MIN >> 1) && m <= (t << 1) {
                t <<= 1;
                p <<= 1;
            }

            m -= t;
            res += p;
        }

        res = if negative { res } else { -res };

        // 截断到 32 位有符号整数范围
        if res > i32::MAX as i64 {
            i32::MAX
        } else if res < i32::MIN as i64 {
            i32::MIN
        } else {
            res as i32
        }
    }
}
```
