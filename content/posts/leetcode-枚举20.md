---
title: "leetcode-枚举20"
date: 2026-07-09T10:05:00+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## k 镜像数字的和

一个 k 镜像数字 指的是一个在十进制和 k 进制下从前往后读和从后往前读都一样的 没有前导 0 的 正 整数。

比方说，9 是一个 2 镜像数字。9 在十进制下为 9 ，二进制下为 1001 ，两者从前往后读和从后往前读都一样。

相反地，4 不是一个 2 镜像数字。4 在二进制下为 100 ，从前往后和从后往前读不相同。

给你进制 k 和一个数字 n ，请你返回 k 镜像数字中 最小 的 n 个数 之和 。


```
impl Solution {
    /// 返回 k 镜像数字中前 n 个最小数之和。
    /// k 镜像数字：十进制和 k 进制均为回文数。
    pub fn k_mirror(k: i32, n: i32) -> i64 {
        let mut count = 0;
        let mut sum = 0i64;
        let mut digit_buffer = [0u8; 200];

        // 按十进制位数递增枚举回文数
        let mut length = 1;
        while count < n {
            // 枚举长度为 length 的所有回文数
            // 奇数长度回文数 (len: 1, 3, 5, ...)
            if length % 2 == 1 {
                let half_len = length / 2;
                let start = 10i64.pow(half_len as u32);
                let end = start * 10;

                for half in start..end {
                    if count >= n { break; }

                    // 构造奇数长度回文数：half + reverse(half / 10)
                    let mut num = half;
                    let mut rev = half / 10;
                    while rev > 0 {
                        num = num * 10 + rev % 10;
                        rev /= 10;
                    }

                    if Self::is_palindrome_in_base(num, k, &mut digit_buffer) {
                        count += 1;
                        sum += num;
                    }
                }
            }

            // 偶数长度回文数 (len: 2, 4, 6, ...)
            if length % 2 == 0 {
                let half_len = length / 2;
                let start = 10i64.pow((half_len - 1) as u32);
                let end = start * 10;

                for half in start..end {
                    if count >= n { break; }

                    // 构造偶数长度回文数：half + reverse(half)
                    let mut num = half;
                    let mut rev = half;
                    while rev > 0 {
                        num = num * 10 + rev % 10;
                        rev /= 10;
                    }

                    if Self::is_palindrome_in_base(num, k, &mut digit_buffer) {
                        count += 1;
                        sum += num;
                    }
                }
            }

            length += 1;
        }

        sum
    }

    /// 检查十进制数 x 在 k 进制下是否为回文数。
    #[inline]
    fn is_palindrome_in_base(mut x: i64, base: i32, buffer: &mut [u8]) -> bool {
        let mut len = 0;

        // 转换为 base 进制数字，存入 buffer
        while x > 0 {
            buffer[len] = (x % base as i64) as u8;
            x /= base as i64;
            len += 1;
        }

        // 判断是否回文
        let mut left = 0;
        let mut right = len - 1;
        while left < right {
            if buffer[left] != buffer[right] {
                return false;
            }
            left += 1;
            right -= 1;
        }

        true
    }
}
```
