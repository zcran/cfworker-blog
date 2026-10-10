---
title: "leetcode-动态规划102"
date: 2026-10-05T20:40:01+08:00
tags: ["leetcode", "动态规划"]
draft: false
---


## 统计打字方案数

Alice 在给 Bob 用手机打字。数字到字母的 对应 如下图所示。
![](../pic/1752723054-mfIHZs-image.png)

为了 打出 一个字母，Alice 需要 按 对应字母 i 次，i 是该字母在这个按键上所处的位置。

· 比方说，为了按出字母 's' ，Alice 需要按 '7' 四次。类似的， Alice 需要按 '5' 两次得到字母  'k' 。
· 注意，数字 '0' 和 '1' 不映射到任何字母，所以 Alice 不 使用它们。

但是，由于传输的错误，Bob 没有收到 Alice 打字的字母信息，反而收到了 按键的字符串信息 。

· 比方说，Alice 发出的信息为 "bob" ，Bob 将收到字符串 "2266622" 。

给你一个字符串 pressedKeys ，表示 Bob 收到的字符串，请你返回 Alice 总共可能发出多少种文字信息 。

由于答案可能很大，将它对 10^9 + 7 取余 后返回。


```
const MOD: i64 = 1_000_000_007;

impl Solution {
    pub fn count_texts(pressed_keys: String) -> i32 {
        let n = pressed_keys.len();
        // dp3[k]: 连续 k 次按同一个 3 字母按键（2/3/4/5/6/8）的可能方案数
        // dp4[k]: 连续 k 次按同一个 4 字母按键（7/9）的可能方案数
        // 递推类似爬楼梯，dp3 每次可拆 1~3 个字母，dp4 可拆 1~4 个字母
        let mut dp3 = vec![1i64; n + 1];
        let mut dp4 = vec![1i64; n + 1];
        for k in 1..=n {
            let mut s3 = 0i64;
            let mut s4 = 0i64;
            // 累加前 1~3 项
            for t in 1..=3.min(k) {
                s3 += dp3[k - t];
            }
            // 累加前 1~4 项
            for t in 1..=4.min(k) {
                s4 += dp4[k - t];
            }
            dp3[k] = s3 % MOD;
            dp4[k] = s4 % MOD;
        }

        let keys = pressed_keys.as_bytes();
        let mut res = 1i64;
        let mut cnt = 1usize;

        for i in 1..=n {
            // 判断当前段是否结束：i == n 或字符变化
            if i < n && keys[i] == keys[i - 1] {
                cnt += 1;
                continue;
            }
            // 段结束：根据按键类型（7/9 为 4 字母，其余为 3 字母）累乘方案数
            let is_four = keys[i - 1] == b'7' || keys[i - 1] == b'9';
            res = res * if is_four { dp4[cnt] } else { dp3[cnt] } % MOD;
            cnt = 1;
        }

        res as i32
    }
}
```
