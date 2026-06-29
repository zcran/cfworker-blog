---
title: "leetcode-数论51"
date: 2026-06-20T11:09:57+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 最小可整除数位乘积 II


给你一个字符串 num ，表示一个 正 整数，同时给你一个整数 t 。

如果一个整数 没有 任何数位是 0 ，那么我们称这个整数是 无零 数字。

请你返回一个字符串，这个字符串对应的整数是大于等于 num 的 最小无零 整数，且 各数位之积 能被 t 整除。如果不存在这样的数字，请你返回 "-1" 。

```
impl Solution {
    pub fn smallest_number(num: String, mut t: i64) -> String {
        // 检查 t 是否包含大于 7 的质因子
        // 因为数字 2-9 的质因子只可能是 2, 3, 5, 7
        let mut temp = t;
        for i in (2..=9).rev() {
            while temp % i == 0 {
                temp /= i;
            }
        }
        if temp > 1 {
            return "-1".to_string();
        }

        let n = num.len();
        let chars: Vec<char> = num.chars().collect();

        // 计算前缀剩余因子：left_t[i] 表示处理完前 i 个字符后，还需要乘的因子
        let mut left_t = vec![0i64; n + 1];
        left_t[0] = t;
        let mut first_zero = n - 1;

        for i in 0..n {
            if chars[i] == '0' {
                first_zero = i;
                break;
            }
            let digit = (chars[i] as u8 - b'0') as i64;
            left_t[i + 1] = left_t[i] / Self::gcd(left_t[i], digit);
        }

        // 如果原数字的数位之积已经是 t 的倍数，直接返回
        if left_t[n] == 1 {
            return num;
        }

        // 尝试构造和原数字相同长度的答案
        let mut s: Vec<char> = chars.clone();
        for i in (0..=first_zero).rev() {
            // 从当前位开始，尝试增大这一位
            let start_digit = (s[i] as u8 - b'0') + 1;
            for digit in start_digit..=9 {
                s[i] = (b'0' + digit) as char;

                // 计算剩余需要处理的因子
                let mut remaining = left_t[i] / Self::gcd(left_t[i], digit as i64);
                let mut k = 9;

                // 从右向左填充剩余位，使用尽量大的数字
                for j in (i + 1..n).rev() {
                    while remaining % k != 0 {
                        k -= 1;
                    }
                    remaining /= k;
                    s[j] = (b'0' + k as u8) as char;
                }

                // 检查是否成功构造
                if remaining == 1 {
                    return s.iter().collect();
                }
            }
        }

        // 如果相同长度无法构造，答案一定比原数字长
        // 构造一个长度为 n+1 的最小无零数字
        let mut factors: Vec<char> = Vec::new();
        let mut t_copy = t;
        for digit in (2..=9).rev() {
            while t_copy % digit == 0 {
                factors.push((b'0' + digit as u8) as char);
                t_copy /= digit;
            }
        }

        // 补充 '1' 使长度达到 n+1
        let mut result = factors;
        let ones_needed = (n + 1).saturating_sub(result.len());
        result.extend(vec!['1'; ones_needed]);

        // 反转得到最小数字（大数字在前，小数字在后）
        result.reverse();
        result.iter().collect()
    }

    /// 计算最大公约数
    #[inline]
    fn gcd(mut a: i64, mut b: i64) -> i64 {
        while b != 0 {
            let temp = a % b;
            a = b;
            b = temp;
        }
        a
    }
}
```
