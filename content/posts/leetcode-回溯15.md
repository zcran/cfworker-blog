---
title: "leetcode-回溯15"
date: 2026-07-04T10:22:02+08:00
tags: ["leetcode", "回溯"]
draft: false
---


## 复原 IP 地址

有效 IP 地址 正好由四个整数（每个整数位于 0 到 255 之间组成，且不能含有前导 0），整数之间用 '.' 分隔。

例如："0.1.2.201" 和 "192.168.1.1" 是 有效 IP 地址，但是 "0.011.255.245"、"192.168.1.312" 和 "192.168@1.1" 是 无效 IP 地址。

给定一个只包含数字的字符串 s ，用以表示一个 IP 地址，返回所有可能的有效 IP 地址，这些地址可以通过在 s 中插入 '.' 来形成。你 不能 重新排序或删除 s 中的任何数字。你可以按 任何 顺序返回答案。


```
impl Solution {
    pub fn restore_ip_addresses(s: String) -> Vec<String> {
        let s = s.as_bytes();
        let n = s.len();
        let mut result = Vec::new();
        let mut segments = Vec::with_capacity(4);

        // 长度必须在 4 到 12 之间
        if n < 4 || n > 12 {
            return result;
        }

        Self::backtrack(s, 0, 0, &mut segments, &mut result);
        result
    }

    /// 回溯生成所有有效的 IP 地址
    /// - s: 数字字符串的字节数组
    /// - start: 当前解析的起始位置
    /// - parts: 已解析的段数
    /// - segments: 当前存储的四个段
    /// - result: 所有有效的 IP 地址
    fn backtrack(
        s: &[u8],
        start: usize,
        parts: usize,
        segments: &mut Vec<usize>,
        result: &mut Vec<String>,
    ) {
        // 已经分成 4 段
        if parts == 4 {
            if start == s.len() {
                result.push(format!(
                    "{}.{}.{}.{}",
                    segments[0], segments[1], segments[2], segments[3]
                ));
            }
            return;
        }

        // 剪枝：剩余字符太多或太少
        let remaining = s.len() - start;
        let min_need = 4 - parts;
        if remaining < min_need || remaining > min_need * 3 {
            return;
        }

        // 尝试 1 到 3 位数字
        let max_len = std::cmp::min(3, remaining);
        for len in 1..=max_len {
            // 检查前导零
            if len > 1 && s[start] == b'0' {
                break;
            }

            // 计算数字值
            let mut num = 0;
            for &ch in &s[start..start + len] {
                num = num * 10 + (ch - b'0') as usize;
            }

            // 检查范围
            if num > 255 {
                break;
            }

            // 递归
            segments.push(num);
            Self::backtrack(s, start + len, parts + 1, segments, result);
            segments.pop();
        }
    }
}
```
