---
title: "leetcode-位运算63"
date: 2026-09-24T10:33:38+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 大数组元素的乘积

一个非负整数 x 的 强数组 指的是满足元素为 2 的幂且元素总和为 x 的最短有序数组。下表说明了如何确定 强数组 的示例。可以证明，x 对应的强数组是独一无二的。

数字	二进制表示	强数组
1	00001	[1]
8	01000	[8]
10	01010	[2, 8]
13	01101	[1, 4, 8]
23	10111	[1, 2, 4, 16]


我们将每一个升序的正整数 i （即1，2，3等等）的 强数组 连接得到数组 big_nums ，big_nums 开始部分为 [1, 2, 1, 2, 4, 1, 4, 2, 4, 1, 2, 4, 8, ...] 。

给你一个二维整数数组 queries ，其中 queries[i] = [fromi, toi, modi] ，你需要计算 (big_nums[fromi] * big_nums[fromi + 1] * ... * big_nums[toi]) % modi 。

请你返回一个整数数组 answer ，其中 answer[i] 是第 i 个查询的答案。


```
impl Solution {
    pub fn find_products_of_elements(queries: Vec<Vec<i64>>) -> Vec<i32> {
        queries
            .iter()
            .map(|q| {
                // 转换为一维下标（原题给的是 0-based，内部使用 1-based 便于计算）
                let (l, r, m) = (q[0] + 1, q[1] + 1, q[2]);
                let (li, ri) = (Self::locate(l), Self::locate(r));

                let mut res = 1i64;

                // 处理左端点所在数字的贡献
                Self::accumulate(li, l, r, m, &mut res);
                // 处理右端点所在数字的贡献（若与左端点不同）
                if ri != li {
                    Self::accumulate(ri, l, r, m, &mut res);
                }

                // 中间完整数字的贡献：其强数组就是各二进制位，等价于对 2 的 (数位和) 次幂取模
                if ri - li > 1 {
                    let exp = Self::count_pow(ri - 1) - Self::count_pow(li);
                    res = res * Self::pow_mod(2, exp, m) % m;
                }

                res as i32
            })
            .collect()
    }

    /// 累加数字 x 的强数组中，位于 [l, r] 区间内的元素对 res 的乘积贡献
    fn accumulate(x: i64, l: i64, r: i64, m: i64, res: &mut i64) {
        let mut pos = Self::count_one(x - 1); // x 强数组起始位置前的元素个数
        for j in 0..60 {
            if (1i64 << j) & x != 0 {
                pos += 1;
                if pos >= l && pos <= r {
                    *res = *res * (1i64 << j) % m;
                }
            }
        }
    }

    /// 计算 <= x 所有数的数位 1 的总个数
    fn count_one(mut x: i64) -> i64 {
        let (mut res, mut sum) = (0i64, 0i64);
        for i in (0..=60).rev() {
            if (1i64 << i) & x != 0 {
                res += sum * (1i64 << i) + i * (1i64 << (i - 1).max(0)) * (i > 0) as i64;
                sum += 1;
            }
        }
        res + sum
    }

    /// 计算 <= x 所有数的数位对幂的贡献之和（即各数数位和的总和）
    fn count_pow(mut x: i64) -> i64 {
        let (mut res, mut sum) = (0i64, 0i64);
        for i in (0..=60).rev() {
            if (1i64 << i) & x != 0 {
                res += sum * (1i64 << i);
                if i > 0 {
                    res += i * (i - 1) / 2 * (1i64 << (i - 1));
                }
                sum += i;
            }
        }
        res + sum
    }

    /// 快速幂：计算 x^y mod m
    fn pow_mod(mut x: i64, mut y: i64, m: i64) -> i64 {
        let mut res = 1i64;
        x %= m;
        while y > 0 {
            if y & 1 == 1 {
                res = res * x % m;
            }
            x = x * x % m;
            y >>= 1;
        }
        res
    }

    /// 二分查找：第 x 个 big_nums 元素所属的数字
    fn locate(x: i64) -> i64 {
        let (mut l, mut r) = (1i64, 1_000_000_000_000_000i64);
        while l < r {
            let mid = (l + r) >> 1;
            if Self::count_one(mid) >= x { r = mid } else { l = mid + 1 }
        }
        l
    }
}
```
