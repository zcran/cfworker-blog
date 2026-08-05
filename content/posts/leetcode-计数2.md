---
title: "leetcode-计数2"
date: 2026-08-01T10:40:50+08:00
tags: ["leetcode", "计数"]
draft: false
---


## 猜数字游戏

你在和朋友一起玩 猜数字（Bulls and Cows）游戏，该游戏规则如下：

写出一个秘密数字，并请朋友猜这个数字是多少。朋友每猜测一次，你就会给他一个包含下述信息的提示：

猜测数字中有多少位属于数字和确切位置都猜对了（称为 "Bulls"，公牛），
有多少位属于数字猜对了但是位置不对（称为 "Cows"，奶牛）。也就是说，这次猜测中有多少位非公牛数字可以通过重新排列转换成公牛数字。
给你一个秘密数字 secret 和朋友猜测的数字 guess ，请你返回对朋友这次猜测的提示。

提示的格式为 "xAyB" ，x 是公牛个数， y 是奶牛个数，A 表示公牛，B 表示奶牛。

请注意秘密数字和朋友猜测的数字都可能含有重复数字。


```
impl Solution {
    /// 计算猜数字游戏的提示
    ///
    /// # 参数
    /// - `secret`: 秘密数字字符串
    /// - `guess`: 猜测数字字符串
    ///
    /// # 返回
    /// 格式为 "xAyB" 的提示，x 为公牛数，y 为奶牛数
    ///
    /// # 算法思路
    /// 1. 遍历两个字符串，位置相同的数字为公牛（A）
    /// 2. 对于位置不同的数字，统计两个字符串中各数字的出现次数
    /// 3. 奶牛数（B）= 每个数字在 secret 和 guess 中出现次数的较小值之和
    ///
    /// # 复杂度
    /// - 时间：O(n)，n 为字符串长度
    /// - 空间：O(1)，仅使用两个固定长度数组
    pub fn get_hint(secret: String, guess: String) -> String {
        // 将字符串转为字节数组，提高索引效率
        let secret = secret.as_bytes();
        let guess = guess.as_bytes();

        let mut bulls = 0;
        // 统计非公牛数字的出现次数（只有 0-9 十个数字）
        let mut secret_count = [0; 10];
        let mut guess_count = [0; 10];

        // 遍历每个字符，计算公牛并统计剩余数字
        for (&s, &g) in secret.iter().zip(guess.iter()) {
            if s == g {
                bulls += 1;
            } else {
                // 将 ASCII 码转为数字索引（'0' = 48）
                secret_count[(s - b'0') as usize] += 1;
                guess_count[(g - b'0') as usize] += 1;
            }
        }

        // 计算奶牛数：每个数字在两个字符串中出现次数的最小值之和
        let cows: i32 = secret_count
            .iter()
            .zip(guess_count.iter())
            .map(|(&s, &g)| s.min(g))
            .sum();

        // 格式化输出
        format!("{}A{}B", bulls, cows)
    }
}
```
