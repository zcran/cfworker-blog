---
title: "leetcode-二分查找16"
date: 2026-09-28T12:29:11+08:00
tags: ["leetcode", "二分查找"]
draft: false
---


## 最小好进制

以字符串的形式给出 n , 以字符串的形式返回 n 的最小 好进制  。

如果 n 的  k(k>=2) 进制数的所有数位全为1，则称 k(k>=2) 是 n 的一个 好进制 。


```
impl Solution {
    pub fn smallest_good_base(n: String) -> String {
        let num: u128 = n.parse().unwrap();

        // 从最大位数（对应最小进制）开始枚举，找到第一个合法进制即为答案
        // 位数 i 最大为 log2(num) + 1，u128 最多 128 位
        for bits in (2..=127).rev() {
            // 二分查找满足 1 + k + k^2 + ... + k^(bits-1) == num 的 k
            let (mut lo, mut hi) = (2u128, num);
            while lo <= hi {
                let mid = lo + (hi - lo) / 2;

                // 计算 mid 进制下 bits 位全 1 的值，同时检测溢出
                let mut sum = 0u128;
                let mut overflow = false;
                for _ in 0..bits {
                    sum = sum * mid + 1;
                    if sum > num {
                        overflow = true;
                        break;
                    }
                }

                if overflow || sum > num {
                    hi = mid - 1;
                } else if sum < num {
                    lo = mid + 1;
                } else {
                    return mid.to_string();
                }
            }
        }

        // 任意 n > 1 都至少有一个好进制 n-1（对应 "11"）
        (num - 1).to_string()
    }
}
```
