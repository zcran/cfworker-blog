---
title: "leetcode-数学11"
date: 2026-10-07T20:39:28+08:00
tags: ["leetcode", "数学"]
draft: false
---


## 相等的有理数

给定两个字符串 s 和 t ，每个字符串代表一个非负有理数，只有当它们表示相同的数字时才返回 true 。字符串中可以使用括号来表示有理数的重复部分。

有理数 最多可以用三个部分来表示：整数部分 <IntegerPart>、小数非重复部分 <NonRepeatingPart> 和小数重复部分 <(><RepeatingPart><)>。数字可以用以下三种方法之一来表示：

<IntegerPart>
例： 0 ,12 和 123

<IntegerPart><.><NonRepeatingPart>
例： 0.5 , 1. , 2.12 和 123.0001

<IntegerPart><.><NonRepeatingPart><(><RepeatingPart><)>
例： 0.1(6) ， 1.(9)， 123.00(1212)

十进制展开的重复部分通常在一对圆括号内表示。例如：

1 / 6 = 0.16666666... = 0.1(6) = 0.1666(6) = 0.166(66)


```
use std::ops::Add;

/// 有理数（分数）表示，使用分子/分母存储。
/// PartialEq 通过交叉相乘比较，避免约分开销。
#[derive(Debug, Copy, Clone)]
struct Rational {
    up: i32,
    down: i32,
}

impl PartialEq for Rational {
    fn eq(&self, other: &Self) -> bool {
        self.up * other.down == self.down * other.up
    }
}

impl Add for Rational {
    type Output = Self;

    fn add(self, other: Self) -> Self {
        // 任一为 0 时直接返回另一个，减少无谓运算
        if self.up == 0 {
            return other;
        }
        if other.up == 0 {
            return self;
        }
        if self.down == other.down {
            Self { up: self.up + other.up, down: self.down }
        } else {
            Self {
                up: self.up * other.down + other.up * self.down,
                down: self.down * other.down,
            }
        }
    }
}

impl Solution {
    /// 判断两个表示非负有理数的字符串是否相等。
    ///
    /// 有理数形式：`整数[.非重复部分[(重复部分)]]`
    /// 解析为三部分之和：整数部分 + 非重复小数部分 + 重复小数部分。
    /// 重复部分的值 = 重复数字 / (10^len(重复) - 1) / 10^len(非重复)。
    pub fn is_rational_equal(s: String, t: String) -> bool {
        Self::to_rational(&s) == Self::to_rational(&t)
    }

    /// 将字符串解析为 Rational。
    fn to_rational(s: &str) -> Rational {
        let bytes = s.as_bytes();

        // 定位 '.'、'('、')' 的下标（-1 表示不存在）
        let mut dot = None;
        let mut left = None;
        let mut right = None;
        for (i, &c) in bytes.iter().enumerate() {
            match c {
                b'.' => dot = Some(i),
                b'(' => left = Some(i),
                b')' => right = Some(i),
                _ => {}
            }
        }

        // 整数部分
        let int_end = dot.unwrap_or(bytes.len());
        let integer = Rational {
            up: s[..int_end].parse().unwrap(),
            down: 1,
        };

        // 非重复小数部分
        let non_repeating = match dot {
            // 无小数点，或小数点后为空，或小数点后紧跟 '('
            None => Rational { up: 0, down: 1 },
            Some(d) if d + 1 == bytes.len() || left == Some(d + 1) => {
                Rational { up: 0, down: 1 }
            }
            Some(d) => {
                let end = left.unwrap_or(bytes.len());
                Rational {
                    up: s[d + 1..end].parse().unwrap(),
                    down: 10_i32.pow((end - d - 1) as u32),
                }
            }
        };

        // 重复小数部分
        let repeating = match (left, right) {
            (Some(l), Some(r)) => {
                let digits = r - l - 1;
                Rational {
                    up: s[l + 1..r].parse().unwrap(),
                    // 分母 = 非重复部分分母 × (10^重复位数 - 1)
                    down: non_repeating.down * (10_i32.pow(digits as u32) - 1),
                }
            }
            _ => Rational { up: 0, down: 1 },
        };

        integer + non_repeating + repeating
    }
}
```
