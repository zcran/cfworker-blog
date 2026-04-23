---
title: "leetcode-滚动哈希11"
date: 2026-04-17T20:00:31+08:00
tags: ["leetcode", "滚动哈希"]
draft: false
---


## 查找给定哈希值的子串

给定整数 p 和 m ，一个长度为 k 且下标从 0 开始的字符串 s 的哈希值按照如下函数计算：

hash(s, p, m) = (val(s[0]) * p0 + val(s[1]) * p1 + ... + val(s[k-1]) * pk-1) mod m.
其中 val(s[i]) 表示 s[i] 在字母表中的下标，从 val('a') = 1 到 val('z') = 26 。

给你一个字符串 s 和整数 power，modulo，k 和 hashValue 。请你返回 s 中 第一个 长度为 k 的 子串 sub ，满足 hash(sub, power, modulo) == hashValue 。

测试数据保证一定 存在 至少一个这样的子串。

子串 定义为一个字符串中连续非空字符组成的序列。


```
impl Solution {
    /// 快速幂模运算 (x^n % m)
    fn qpow(mut x: i64, mut n: i32, m: i64) -> i64 {
        let mut res = 1 % m;
        while n > 0 {
            if n & 1 == 1 {
                res = (res * x) % m;
            }
            x = (x * x) % m;
            n >>= 1;
        }
        res
    }

    /// 在字符串 s 中查找第一个长度为 k 的子串，使其哈希值等于 hash_value。
    /// 哈希定义：hash = (s[i] * power^0 + s[i+1] * power^1 + ... + s[i+k-1] * power^{k-1}) % modulo
    /// 字符映射：'a' -> 1, 'b' -> 2, ... (通过 `c & 31` 高效实现)
    pub fn sub_str_hash(s: String, power: i32, modulo: i32, k: i32, hash_value: i32) -> String {
        let s = s.as_bytes();                     // 字节数组，便于索引
        let n = s.len();
        let power = power as i64;
        let m = modulo as i64;
        let k = k as usize;
        let target = hash_value as i64;

        // 预计算 power^(k-1) % m，用于移除窗口最右侧字符的贡献
        let p = Self::qpow(power, (k - 1) as i32, m);

        // 字符数值映射闭包：利用 ASCII 码 'a'=97 → 97&31=1，'b'=98→2，...
        let char_val = |c: u8| (c & 31) as i64;

        // 滚动哈希状态
        let mut hash = 0i64;
        let mut ans_left = 0;   // 记录匹配子串的起始索引（题目保证存在）

        // 从右向左扫描，每次窗口左端点 i 递减
        for i in (0..n).rev() {
            // 1. 新字符（成为窗口左端点）进入哈希
            //    公式：hash = hash * power + new_char   (将新字符放在最低位)
            hash = (hash * power + char_val(s[i])) % m;

            let right = i + k - 1;   // 当前窗口的右端点索引
            if right >= n {
                // 窗口尚未形成（长度不足 k），继续扩展
                continue;
            }

            // 2. 当前窗口 [i, right] 已完整，检查哈希是否匹配
            if hash == target {
                ans_left = i;        // 题目保证有解，且 i 递减，所以最终 ans_left 是最左边的匹配
            }

            // 3. 移除即将离开窗口的最右侧字符（为下一个左移做准备）
            //    公式：hash = (hash - right_char * power^(k-1)) % m
            let right_val = char_val(s[right]);
            hash = (hash - right_val * p % m + m) % m;   // +m 保证非负
        }

        // 提取并返回匹配的子串
        String::from_utf8(s[ans_left..ans_left + k].to_vec()).unwrap()
    }
}
```
