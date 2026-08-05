---
title: "leetcode-滑动窗口46"
date: 2026-07-18T11:02:31+08:00
tags: ["leetcode", "滑动窗口"]
draft: false
---


## 使二进制字符串字符交替的最少反转次数

给你一个二进制字符串 s 。你可以按任意顺序执行以下两种操作任意次：

类型 1 ：删除 字符串 s 的第一个字符并将它 添加 到字符串结尾。
类型 2 ：选择 字符串 s 中任意一个字符并将该字符 反转 ，也就是如果值为 '0' ，则反转得到 '1' ，反之亦然。

请你返回使 s 变成 交替 字符串的前提下， 类型 2 的 最少 操作次数 。

我们称一个字符串是 交替 的，需要满足任意相邻字符都不同。

比方说，字符串 "010" 和 "1010" 都是交替的，但是字符串 "0100" 不是。


```
impl Solution {
    pub fn min_flips(s: String) -> i32 {
        let bytes = s.as_bytes();
        let n = bytes.len();

        // 目标交替字符串的两种模式："0101..." 和 "1010..."
        // cost0: 变成以'0'开头的交替串所需翻转次数
        // cost1: 变成以'1'开头的交替串所需翻转次数
        let mut cost0 = 0;
        let mut cost1 = 0;

        for (i, &ch) in bytes.iter().enumerate() {
            let expected0 = if i % 2 == 0 { b'0' } else { b'1' };
            let expected1 = if i % 2 == 0 { b'1' } else { b'0' };

            if ch != expected0 { cost0 += 1; }
            if ch != expected1 { cost1 += 1; }
        }

        // 如果长度为偶数，两种模式的代价取最小即可
        if n % 2 == 0 {
            return cost0.min(cost1);
        }

        // 奇数长度时，要考虑旋转（类型1操作）的影响
        // 旋转相当于重新调整奇偶位置，我们可以通过滑动窗口计算
        let mut ans = cost0.min(cost1);

        // 将字符串复制一份以便处理循环
        let doubled: Vec<u8> = bytes.iter().chain(bytes.iter()).copied().collect();

        // 维护两个滑动窗口的代价
        // 窗口长度为n，每次向右滑动一位，更新代价
        for i in 1..n {
            // 移除离开窗口的字符对代价的影响（位置i-1）
            let leave = bytes[i - 1];
            // 移除时，需要减去它原先对两种模式的贡献
            // 原先在位置0，现在要移除，对于从i开始的新窗口，位置重新编号
            // 更简便的方法：利用已有的cost0/cost1，但需要重新计算
        }

        // 更优解法：利用奇偶长度特性，只需考虑两种情况：
        // 情况1：不旋转，已计算
        // 情况2：旋转后，重新计算代价
        // 由于n为奇数，旋转会影响奇偶性映射

        // 重新计算，但用更高效的方法
        // 对于奇数n，旋转后的交替模式可以看作：
        // 从某个位置开始，交替模式不变，但奇偶位置互换
        // 实际上，旋转k次后，新的位置j对应的原位置是(j+k)%n

        // 简单但高效的方法：计算两种可能的交替模式在所有循环移位下的最小代价
        // 对每个可能的分割点，计算前后两段分别匹配哪种模式

        let mut min_cost = cost0.min(cost1);
        let mut prefix0 = vec![0; n + 1];
        let mut prefix1 = vec![0; n + 1];

        for i in 0..n {
            let expected0 = if i % 2 == 0 { b'0' } else { b'1' };
            let expected1 = if i % 2 == 0 { b'1' } else { b'0' };

            prefix0[i + 1] = prefix0[i] + if bytes[i] != expected0 { 1 } else { 0 };
            prefix1[i + 1] = prefix1[i] + if bytes[i] != expected1 { 1 } else { 0 };
        }

        // 考虑分割点i：左边[0..i]匹配模式0，右边[i+1..n-1]匹配模式1
        for i in 0..n - 1 {
            // 左边部分长度 i+1，右边长度 n-i-1
            // 左边匹配模式0（从0开始），右边匹配模式1（从0开始，但因为整体移位，需要调整）
            // 对于右边，如果从0开始匹配模式0，但整体旋转后奇偶性反转，所以右边要匹配相反的
            let left_cost = prefix0[i + 1];
            let right_cost = (prefix1[n] - prefix1[i + 1]);
            min_cost = min_cost.min(left_cost + right_cost);

            // 左边匹配模式1，右边匹配模式0
            let left_cost2 = prefix1[i + 1];
            let right_cost2 = (prefix0[n] - prefix0[i + 1]);
            min_cost = min_cost.min(left_cost2 + right_cost2);
        }

        min_cost
    }
}
```
