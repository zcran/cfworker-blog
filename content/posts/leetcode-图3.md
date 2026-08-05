---
title: "leetcode-图3"
date: 2026-07-27T10:01:35+08:00
tags: ["leetcode", "图"]
draft: false
---


## 破解保险箱

有一个需要密码才能打开的保险箱。密码是 n 位数, 密码的每一位都是范围 [0, k - 1] 中的一个数字。

保险箱有一种特殊的密码校验方法，你可以随意输入密码序列，保险箱会自动记住 最后 n 位输入 ，如果匹配，则能够打开保险箱。

· 例如，正确的密码是 "345" ，并且你输入的是 "012345" ：
    输入 0 之后，最后 3 位输入是 "0" ，不正确。
    输入 1 之后，最后 3 位输入是 "01" ，不正确。
    输入 2 之后，最后 3 位输入是 "012" ，不正确。
    输入 3 之后，最后 3 位输入是 "123" ，不正确。
    输入 4 之后，最后 3 位输入是 "234" ，不正确。
    输入 5 之后，最后 3 位输入是 "345" ，正确，打开保险箱。

在只知道密码位数 n 和范围边界 k 的前提下，请你找出并返回确保在输入的 某个时刻 能够打开保险箱的任一 最短 密码序列 。


```
impl Solution {
    pub fn crack_safe(n: i32, k: i32) -> String {
        let n = n as usize;
        let k = k as usize;
        let total = k.pow(n as u32);          // 所有可能密码的数量
        let prefix_count = k.pow(n as u32 - 1); // 前缀数量

        // 记录每个前缀还剩多少条未使用的边（即下一个数字）
        let mut remain = vec![k as i8; prefix_count];

        // 结果序列长度 = 总密码数 + n - 1
        let mut ans = vec![b'0'; total + n - 1];

        // 当前节点（前缀）的编号
        let mut node = 0;

        // 前 n-1 位初始化为全 0
        for i in 0..n - 1 {
            ans[i] = b'0';
        }

        // Hierholzer 算法构造 De Bruijn 序列
        for i in n - 1..ans.len() {
            // 当前节点还有剩余边可走
            let next_digit = remain[node] - 1;
            remain[node] = next_digit;  // 使用一条边

            // 将当前选择的数字写入结果
            ans[i] = (next_digit + b'0' as i8) as u8;

            // 计算下一个节点（前缀）
            // 公式: (node * k + next_digit) % prefix_count
            let digit_val = next_digit as usize;
            let first_digit = (ans[i - (n - 1)] - b'0') as usize;
            node = (node * k + digit_val) - first_digit * prefix_count;
        }

        unsafe { String::from_utf8_unchecked(ans) }
    }
}
```
