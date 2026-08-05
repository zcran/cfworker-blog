---
title: "leetcode-枚举37"
date: 2026-07-09T10:05:01+08:00
tags: ["leetcode", "枚举"]
draft: false
---


## 根据限制分割消息

给你一个字符串 message 和一个正整数 limit 。

你需要根据 limit 将 message 分割 成一个或多个 部分 。每个部分的结尾都是 "<a/b>" ，其中 "b" 用分割出来的总数 替换， "a" 用当前部分所在的编号 替换 ，编号从 1 到 b 依次编号。除此以外，除了最后一部分长度 小于等于 limit 以外，其他每一部分（包括结尾部分）的长度都应该 等于 limit 。

你需要确保分割后的结果数组，删掉每部分的结尾并 按顺序 连起来后，能够得到 message 。同时，结果数组越短越好。

请你返回 message  分割后得到的结果数组。如果无法按要求分割 message ，返回一个空数组。


```
impl Solution {
    pub fn split_message(message: String, limit: i32) -> Vec<String> {
        let n = message.len();
        let limit = limit as usize;

        // 预计算 1..=n 每个数的十进制位数
        let mut digits = vec![0; n + 1];
        for i in 1..=n {
            let mut x = i;
            let mut cnt = 0;
            while x > 0 {
                cnt += 1;
                x /= 10;
            }
            digits[i] = cnt;
        }

        // 枚举分割的份数 X
        for parts in 1..=n {
            // 尾部格式为 "<X/X>" 的长度，这部分长度至少为 5（即 <1/1>）
            let tail_len_last = 3 + 2 * digits[parts];
            if tail_len_last > limit {
                continue; // 尾部太长，无法容纳任何内容
            }

            // 计算前 parts-1 份能容纳的字符总数
            let mut total_content = 0;
            let mut i = 1;
            while i < parts {
                let j = (i * 10).min(parts);
                let count = j - i; // 位数相同的数字个数
                // 当前数字的位数和 parts 的位数之和，加上 '<' '/' '>' 共3个字符
                let each = limit as isize - 3 - digits[parts] as isize - digits[i] as isize;
                if each < 0 {
                    total_content = 0;
                    break; // 容量不够，此 parts 不可行
                }
                total_content += each * count as isize;
                i = j;
            }

            // 如果总容量不够容纳消息，或者剩余字符数为0或超出最后一份容量，跳过
            if total_content < 0 || total_content as usize >= n {
                continue;
            }
            let last_content = n - total_content as usize;
            if last_content == 0 || last_content > limit - tail_len_last {
                continue;
            }

            // 构建结果
            let mut ans = Vec::with_capacity(parts);
            let mut pos = 0;

            for a in 1..=parts {
                // 构造尾部
                let tail = format!("<{}/{}>", a, parts);
                let tail_len = tail.len();

                // 内容长度：前 parts-1 份必须恰好等于 limit - tail_len
                let content_len = if a == parts {
                    last_content
                } else {
                    limit - tail_len
                };

                // 取出对应子串并拼接尾部
                ans.push(format!("{}{}", &message[pos..pos + content_len], tail));
                pos += content_len;
            }

            return ans;
        }

        vec![]
    }
}
```
