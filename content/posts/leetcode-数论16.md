---
title: "leetcode-数论16"
date: 2026-06-20T11:09:55+08:00
tags: ["leetcode", "数论"]
draft: false
---


## 一个区间内所有数乘积的缩写


给你两个正整数 left 和 right ，满足 left <= right 。请你计算 闭区间 [left, right] 中所有整数的 乘积 。

由于乘积可能非常大，你需要将它按照以下步骤 缩写 ：

1.统计乘积中 后缀 0 的数目，并 移除 这些 0 ，将这个数目记为 C 。
    · 比方说，1000 中有 3 个后缀 0 ，546 中没有后缀 0 。

2.将乘积中剩余数字的位数记为 d 。如果 d > 10 ，那么将乘积表示为 <pre>...<suf> 的形式，其中 <pre> 表示乘积最 开始 的 5 个数位，<suf> 表示删除后缀 0 之后 结尾的 5 个数位。如果 d <= 10 ，我们不对它做修改。
    · 比方说，我们将 1234567654321 表示为 12345...54321 ，但是 1234567 仍然表示为 1234567 。

3.最后，将乘积表示为 字符串 "<pre>...<suf>eC" 。
    · 比方说，12345678987600000 被表示为 "12345...89876e5" 。

请你返回一个字符串，表示 闭区间 [left, right] 中所有整数 乘积 的 缩写 。

```
impl Solution {
    pub fn abbreviate_product(left: i32, right: i32) -> String {
        let mut log_ans = 0.0f64;
        let mut cnt2 = 0i64;
        let mut cnt5 = 0i64;

        // 第一遍遍历：计算总的对数值和因子2、5的数量
        for i in left..=right {
            let mut x = i;
            while x % 2 == 0 {
                cnt2 += 1;
                x /= 2;
            }
            while x % 5 == 0 {
                cnt5 += 1;
                x /= 5;
            }
            log_ans += (i as f64).log10();
        }

        let cnt0 = cnt2.min(cnt5);
        let total_digits = (log_ans + 1e-9).floor() as i64 + 1;

        // 如果总位数减去后缀0的个数大于10，需要缩写
        if total_digits - cnt0 > 10 {
            // 使用高精度方法计算前5位
            // 方法：计算乘积的前5位，通过将每个数除以10的幂来保持精度
            let mut mantissa = 1.0f64;

            for i in left..=right {
                mantissa *= i as f64;

                // 当mantissa太大时，缩小它
                while mantissa >= 100000.0 {
                    mantissa /= 10.0;
                }
            }

            // 现在mantissa在[1, 100000)范围内
            // 需要将其放大到[10000, 100000)以获取前5位
            while mantissa < 10000.0 && mantissa > 0.0 {
                mantissa *= 10.0;
            }

            let first = mantissa.floor() as i32;

            // 计算后5位（移除cnt0个因子2和因子5）
            let mut need2 = cnt0;
            let mut need5 = cnt0;
            let mut last = 1i64;

            for i in left..=right {
                let mut x = i;
                while need2 > 0 && x % 2 == 0 {
                    need2 -= 1;
                    x /= 2;
                }
                while need5 > 0 && x % 5 == 0 {
                    need5 -= 1;
                    x /= 5;
                }
                last = (last * x as i64) % 100000;
            }

            // 格式化后5位（补零到5位）
            let s_last = format!("{:05}", last);

            format!("{}...{}e{}", first, s_last, cnt0)
        } else {
            // 总位数不多，直接计算完整乘积
            let mut need2 = cnt0;
            let mut need5 = cnt0;
            let mut ans = 1i64;

            for i in left..=right {
                let mut x = i;
                while need2 > 0 && x % 2 == 0 {
                    need2 -= 1;
                    x /= 2;
                }
                while need5 > 0 && x % 5 == 0 {
                    need5 -= 1;
                    x /= 5;
                }
                ans *= x as i64;
            }

            format!("{}e{}", ans, cnt0)
        }
    }
}
```
