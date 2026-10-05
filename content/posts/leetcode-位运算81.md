---
title: "leetcode-位运算81"
date: 2026-09-24T10:33:39+08:00
tags: ["leetcode", "位运算"]
draft: false
---


## 重新排列后的最大按位异或值

给你两个长度均为 n 的二进制字符串 s 和 t。

你可以按任意顺序 重新排列 t 中的字符，但 s 必须保持不变。

返回一个长度为 n 的 二进制字符串，表示将 s 与重新排列后的 t 进行按位 异或 (XOR) 运算所能获得的 最大 整数值。


```
impl Solution {
    pub fn maximum_xor(s: String, t: String) -> String {
        // 贪心：逐位为 s 匹配能让 XOR 结果为 1 的 t 字符。
        // 对 s 的当前位 x：
        //   - 若 t 中还有与 x 不同的字符（x^1），用它，使该位结果为 1
        //   - 否则只能用相同字符 x，该位结果为 0
        // t 可任意重排，因此只需统计 t 中 0/1 的数量。

        let n = t.len();
        let cnt1 = t.bytes().filter(|&b| b == b'1').count();
        // left[0] = 剩余 0 的个数，left[1] = 剩余 1 的个数
        let mut left = [n - cnt1, cnt1];

        // 原地构造结果，直接覆盖 s
        let mut result = s.into_bytes();
        for ch in result.iter_mut() {
            let x = (*ch - b'0') as usize;
            let want = x ^ 1; // 想让结果为 1，需要 t 中该位为 want
            if left[want] > 0 {
                left[want] -= 1;
                *ch = b'1';
            } else {
                left[x] -= 1;
                *ch = b'0';
            }
        }

        // 字节序列都是 ASCII，转回 String 是安全的
        String::from_utf8(result).unwrap()
    }
}
```
